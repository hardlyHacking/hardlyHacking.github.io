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

Adding the given script is simple enough.

```html
<script src="https://apis.google.com/js/platform.js" async defer></script>
```

As normal, we begin with providing Firebase to our application.

```javascript
import React from "react"
import ReactDom from "react-dom"
import "./styles/global.scss"
import App from "./pages/_app"
import FirebaseContext from "./util/firebaseContext"
import Firebase from "./util/firebase"


ReactDom.render(
  <FirebaseContext.Provider value={new Firebase()}>
    <App />
  </FirebaseContext.Provider>,
  document.getElementById("root")
)
```

The App then loads the `gapi` script and prepares it for use by other components.

Note the `/* global gapi */` which lets babel and other JS compilers know that the variable is loaded globally.

The `init` function returns a promise, so it's important to wait for the promise to load before saving an instance of `gapi`.

Note that, since we're passing `gapi` to child components which will make use of it, we use the `render` method in react router to pass the property on.

```javascript
/* global gapi */
import React, { useEffect, useState } from 'react'
import Navbar from './navbar'
import DashboardPage from './dashboard'
import IndexPage from './index'

function App(props) {

  const [googleApi, setGoogleApi] = useState(null)
  const [isSignedIn, setIsSignedIn] = useState(false)

  useEffect(() => {
    window.gapi.load('auth2', function () {
      gapi.auth2.init({
        client_id: '<YOUR_CLIENT_ID>.apps.googleusercontent.com'
      }).then(() => {
        const auth2 = gapi.auth2.getAuthInstance()

        // Needed to check if they're opening a new tab having already been signed in
        // The listener below only listens to *future* changes
        if (auth2.isSignedIn.get()) {
          setIsSignedIn(true)
        }

        // Listen to future auth changes
        auth2.isSignedIn.listen((isLoggedIn) => {
          setIsSignedIn(isLoggedIn)
        })

        setGoogleApi(auth2)
      })
    })
  }, [])

  return (
    <Router>

      <Navbar
        color="primary"
        spaced={true}
        googleApi={googleApi}
        isSignedIn={isSignedIn}
      />

      <Switch>
        <Route exact path="/" component={IndexPage} />

        <Route exact path="/dashboard"
          render={(props) => <DashboardPage {...props} googleApi={googleApi} />} />

        <Route
          component={({ location }) => {
            return (
              <div
                style={{
                  padding: "50px",
                  width: "100%",
                  textAlign: "center"
                }}
              >
                The page <code>{location.pathname}</code> could not be
                found.
              </div>
            )
          }}
        />
      </Switch>
    </Router >
  )
```

Remember that React components don't re-render on prop changes, but only on state changes.

To make sure that our Navbar re-renders when the gapi loads, and when the user logs in or logs out, we desugar `props` to the individual variables passed in, and set up an effect hook that depends on the two we care about - in this case, `googleApi` and `isSignedIn`.

Since we're using the google API to render our log in button, it's important to call `signin2` to re-render anytime the user logs out. Simply setting a conditional visibility doesn't seem to work.

Lastly, we can conveniently pipe the data from `gapi` into Firebase with the `signInWithCredential` method, which then gives us access to [all the data that we'd normally expect](https://firebase.google.com/docs/reference/js/firebase.User#properties).

```javascript
const Navbar = ({
  color,
  spaced,
  googleApi,
  isSignedIn,
}) => {
  const [menuOpen, setMenuOpen] = useState(false)
  const router = useRouter()
  const firebase = useContext(FirebaseContext)

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
  }, [googleApi, isSignedIn])

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