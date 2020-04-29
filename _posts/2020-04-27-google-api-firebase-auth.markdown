---
layout: post
title: 'Google API + Firebase Auth'
date: '2020-04-27 23:34:40'
tags:
- Google API
- Firebase Auth
- React.js
---

## Motivation

A while back, I asked my friend [Arkadiy](https://twitter.com/arkadiyt) to help me debug [Jumpy](https://jumpy.dev), but curiously he couldn't even login. Arkadiy is my favorite kind of person - not only is he a brilliant engineer, but he's in many ways a walking edge case. Wonder if your website works under a VPN with numerous ad-blockers and privacy-conscious plugins installed? This is the kind of user you need.

Evidently, my website did not work under these conditions. Specifically, [Firebase Auth](https://firebase.google.com/docs/auth) does not work unless the user [enables 3rd party cookies](https://stackoverflow.com/questions/48757757/use-google-firebase-authentication-without-3rd-party-cookies). As Jumpy (initially, at least) is going to be a consumer-facing tool, this limitation is pretty alarming. Virtually every ad blocker prevents third party cookies (in 2019, [over a quarter of internet traffic](https://www.statista.com/statistics/804008/ad-blocking-reach-usage-us/) came from users with ad-blockers installed) - and some browsers like [Brave](https://brave.com/) do so as well.

Curiously, [Google Sign In via Firebase](https://firebase.google.com/docs/auth/web/google-signin) is a completely separate product than the traditional Google API [Sign in With Google](https://developers.google.com/identity/sign-in/web/sign-in?authuser=1). Why they can't simply unite the two I'll never know, but this is a guide to wrapping Firebase Auth around Sign in With Google. This way, we don't have to redo any of our backend endpoints.

While the [getting started](https://developers.google.com/identity/sign-in/web) guide and the [basic integration](https://developers.google.com/identity/sign-in/web/sign-in) docs work, they rely more on HTML processing and global script loading than modern javascript conventions. Integrating this SDK inside a modern React app proved to be slightly more challenging.

## Integration

Google recommends doing something like the following.

```html
<script src="https://apis.google.com/js/platform.js" async defer></script>
```

Unfortunately, makes it messy for me later on down the road. Instead, I decided to initialize it entirely with Javascript, so that by the time I launch my main React App, googleApi is ready to go and loaded. The other benefit of doing it this way is that we can use the Context.Provider and Consumer model to access googleApi.

```javascript
function loadGapiAndAfterwardsInitAuth() {
  const script = document.createElement('script')
  script.src = 'https://apis.google.com/js/platform.js'
  script.async = true
  script.defer = true
  script.onload = initAuth
  const meta = document.createElement('meta')
  meta.name = 'google-signin-client_id'
  meta.content = '79407791349-ar4ivj4dimqr1ocov49f6u7hq484qh59.apps.googleusercontent.com'
  document.head.appendChild(meta)
  document.head.appendChild(script)
}

loadGapiAndAfterwardsInitAuth()
```

Then, once platform.js is loaded and has fetched the regular google apis, we are ready to launch our react app.

The `init` function returns a promise, so it's important to wait for the promise to load before saving an instance of `googleAuth`.

```javascript
function initAuth() {
  window.gapi.load('auth2', function () {
    window.gapi.auth2.init({
      client_id: '79407791349-ar4ivj4dimqr1ocov49f6u7hq484qh59.apps.googleusercontent.com'
    }).then(googleAuth => {
      if (googleAuth) {
        ReactDom.render(
          <GoogleAuthContext.Provider value={googleAuth}>
            <FirebaseContext.Provider value={new Firebase()}>
              <App />
            </FirebaseContext.Provider>
          </GoogleAuthContext.Provider>,
          document.getElementById('root')
        )
      }
    })
  })
}
```

We create the context as normal, with some helper methods like `withGoogleAuth`:

```javascript
import React from 'react'

const GoogleAuthContext = React.createContext(null)

export const withGoogleAuth = Component => props => (
  <GoogleAuthContext.Consumer>
    {googleAuth => <Component {...props} googleApi={googleAuth} />}
  </GoogleAuthContext.Consumer>
)

export default GoogleAuthContext
```

One benefit to doing it this way is that we avoid the unnecessary `/* global gapi */` that you'll find with other implementations. Since everything is loaded into the react scope, we don't need any mysterious global variables polluting up our namespace.

```javascript
/* global gapi */
import React, { useContext, useEffect, useState } from 'react'
import Navbar from './navbar'
import DashboardPage from './dashboard'
import GoogleAuthContext from './googleauthContext'

function App(props) {
  const googleApi = useContext(GoogleAuthContext)
  const [isSignedIn, setIsSignedIn] = useState(false)

  useEffect(() => {
    // Needed to check if they're opening a new tab having already been signed in
    // The listener below only listens to *future* changes
    setIsSignedIn(googleApi.isSignedIn.get())

    // Listen to future auth changes
    auth2.isSignedIn.listen((isLoggedIn) => {
      setIsSignedIn(isLoggedIn)
    })
  }, [])

  return (
    <Router>

      <Navbar
        color="primary"
        spaced={true}
        isSignedIn={isSignedIn}
      />

      <Switch>
        <Route exact path="/" component={IndexPage} />

        <Route exact path="/dashboard" component={DashboardPage} />
      </Switch>
    </Router >
  )
```

Since we're using the google API to render our log in button, it's important to call `signin2` to re-render anytime the user logs out. Simply setting a conditional visibility doesn't seem to work.

Lastly, we can conveniently pipe the data from `gapi` into Firebase with the `signInWithCredential` method, which then gives us access to [all the data that we'd normally expect](https://firebase.google.com/docs/reference/js/firebase.User#properties).

```javascript
const Navbar = ({
  color,
  spaced,
  isSignedIn,
}) => {
  const [menuOpen, setMenuOpen] = useState(false)
  const router = useRouter()
  const firebase = useContext(FirebaseContext)
  const googleApi = useContext(GoogleAuthContext)

  useEffect(() => {
    if (!isSignedIn && googleApi !== null) {
      window.gapi.load('signin2', function () {
        var opts = {
          longtitle: true,
          onfailure: signInWithGoogle,
          onsuccess: signInWithGoogle,
        }
        gapi.signin2.render('loginButtonGoogle', opts)
      })
    }
  }, [isSignedIn])

  useEffect(() => {
    firebase.auth().onAuthStateChanged(async user => {
      if (user) {
        const token = await firebase.currentUser().getIdToken()
        const data = {
          displayName: user.displayName,
          email: user.email,
          photoURL: user.photoURL,
          userId: user.uid,
        }
        // Send the data to your backend
      }
    })
  }, [])

  const signInWithGoogle = async (googleUser) => {
    const idToken = googleUser.getAuthResponse().id_token
    const credential = firebase.createCredential(idToken)

    await firebase.signInWithCredential(credential)
  }

  ...
}
```

## Other Useful Links

Some other useful links:

* [Google sign in JS API docs](https://developers.google.com/identity/sign-in/web/reference#gapiauth2authresponse)
* [Customizing the look of the sign in with Google button](https://developers.google.com/identity/sign-in/web/build-button)
* [Authenticate Sign in with Google on the backend](https://developers.google.com/identity/sign-in/web/backend-auth)
* [A similar guide for Vue](https://medium.com/@vanderlin/google-api-firebase-auth-1b18416b4e74)