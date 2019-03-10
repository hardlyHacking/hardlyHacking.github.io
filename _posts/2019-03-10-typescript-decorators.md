---
layout: post
title: Decorators for Google Cloud Functions
date: '2019-03-10 13:36:30'
tags:
- Typescript
- Serverless
---

One of the first problems new users to Google Cloud Functions face is that hosting an express app for each function is really not a good idea. Although express has a great set of middleware, authentication options, and other nifty features, it really defeats the purpose of serverless as a framework, and you're left in this awkward middle-space where you get none of the real benefits of a managed server while losing out on the benefits of a real serverless API.

Once we move away from something like express, however, we encounter a new issue: the lack of middleware / authentication. Google Cloud Functions obviously integrates well with something like Firebase Auth or other such options, but the lack of middleware was still problematic for us at Skip. In particular, given that every cloud function takes only two things - a request and a response object as parameters - we were left to validate basic JSON on our own.

Very early on, however, we decided to move to typescript, and that has since provided us all the answers we need to solve these sorts of problems. Typescript, unfortunately, does not preserve types at runtime. In fact, to maintain backwards compatibility with Javascript, this feature has explicitly been called out as a [non-goal](https://github.com/Microsoft/TypeScript/issues/1573#issuecomment-68374376) for Typescript, despite many [calls](https://github.com/Microsoft/TypeScript/issues/1573) for it to be included. So besides the javascript primitives (`number`, `string`, `boolean`, and `object`), only custom classes are protected against type erasure.

Using this basic idea, I came up with a fairly simple validation mechanism (wrapped in a nice decorator) to have run-time enforcement. One small benefit is that, since it's not a heavy bloated library, it doesn't affect our [cold start time](https://mikhail.io/2018/08/serverless-cold-start-war/) nearly as much as some of the heavy-weight options.

So let's take a look at a very simple `post` decorator which can wrap our requests and simply enforce that the method type is in fact a post.

```typescript
export function post() {
    return function(_target, _key, descriptor) {
        const originalMethod = descriptor.value
        descriptor.value = function() {
            const req = arguments[0] as functions.Request
            const res = arguments[1] as functions.Response

            if (req.method !== 'POST') {
                console.error(`Incorrect http method`)
                return res.status(404).json({})
            }

            const result = originalMethod.apply(this, arguments)
            return result
        }
        return descriptor
    }
}
```

A pretty simple method decorator that typescript allows for. This means our `HelloWorldApi.ts` would look something like this:

```typescript
import functions = require('firebase-functions')

class HelloWorldApi {
	@post()
	static async postHelloWorld(req, res) {
		// Would be nice if all of the validation was handled auto-magically!
		const repetition = req.body.repetition
		if (!Number.isInteger(repetition)) {
			return res.status(400).json({ error: 'Invalid field provided' })
		}

		const msg = req.body.message
		if (typeof msg !== 'string') {
			return res.status(400).json({ error: 'Invalid field provided' })
		}


		res.status(200).json({ message: msg, times: repetition })
	}
}

export const postHelloWorld = functions.https.onRequest(HelloWorldApi.postHelloWorld)
```

And our `index.ts` file then exports our new functions, as normal.

Going back to our initial problem of run-time JSON type validation, we want our `post` (and `get`) decorators to take an object, like `HelloWorldRequest`, with its own validation definition, as a parameter, and at run-time run the validation against the input received.

To support that, we would have to modify our `post` decorator slightly, with the addition of the following:

* Add a parameter where the keys would be JSON strings, and the values would be `Validator` objects that have their own rules
* For each key in the given object parameter, run the corresponding validation step

```typescript
// Note that the post decorator now accepts an object as a parameter
// Each value in the object is a BaseValidator (defined below)
export function post(parameters: { [name: string]: BaseValidator } = {}) {
    return function(_target, _key, descriptor) {
        const originalMethod = descriptor.value
        descriptor.value = function() {
            const req = arguments[0] as functions.Request
            const res = arguments[1] as functions.Response

            if (req.method !== 'POST') {
                console.error(`Incorrect http method`)
                return res.status(404).json({})
            }

            // Iterate through each BaseValidator and make sure the given input can be deserialized
            // If not, we return a 400, leaving our actual functions clean and DRY
            for (const param of Object.keys(parameters)) {
                try {
                    req.body[param] = parameters[param].deserialize(req.body[param])
                } catch (error) {
                    return res.status(400).json({
                        success: false,
                        message: 'Invalid parameters provided',
                    })
                }
            }

            // tslint:disable-next-line
            const result = originalMethod.apply(this, arguments)
            return result
        }
        return descriptor
    }
}
```

So what is a `BaseValidator`, and how does it work?

As we stated above, in order for this to work and not be type-erased at runtime, this _must_ be a class. Specifically, an abstract class.

```typescript
// Base validator class that all will extend
export abstract class BaseValidator {
    // Determine if a given input can be deserialized
    abstract validate(input: any): boolean

    // Perform the parsing on a given input
    // This method assumes the validation of an input has already been performed!
    // Calling this method on un-validated input will produce undefined behavior!
    abstract parse(input: string | number | boolean | object): string | number | boolean | object

    // Safely checks whether a given input can be deserialized / parsed
    // If so, it calls the parse method
    // Otherwise, it returns null
    deserialize(input: string | number | boolean | object): string | number | boolean | object | null {
        if (this.validate(input)) {
            return this.parse(input)
        }
        throw new Error('Invalid input given')
    }
}
```

So if we wanted an `IntValidator` that would verify that a given field was an integer, we could do this by extending `BaseValidator` pretty easily:

```typescript
import { BaseValidator } from './baseValidator'

// This class will determine if the input of #validate is an int
class BaseIntValidator extends BaseValidator {
    constructor() {
        super()
    }

    validate(input: any): boolean {
        return (typeof input === 'number' || typeof input === 'string') && Number.isInteger(Number.parseFloat(input as string))
    }

    parse(input: string | number): number {
        return Number.parseInt(input as string)
    }
}

export const IntValidator = new BaseIntValidator()
```

`StringValidator` is even easier:

```typescript
import { BaseValidator } from './baseValidator'

// This class will determine if the input of #validate is a string
export class BaseStringValidator extends BaseValidator {
    constructor() {
        super()
    }

    validate(input: any): boolean {
        return typeof input === 'string'
    }

    parse(input: string): string {
        return input
    }
}

export const StringValidator = new BaseStringValidator() 
```

With these new validators and post decorator, we can go back to our previously defined `postHelloWorld` and make it much cleaner.

```typescript
import functions = require('firebase-functions')

import { IntegerValidator } from '../parsers/integerValidator'
import { StringValidator } from '../parsers/stringValidator'

class HelloWorldApi {
	@post({ repetition: IntegerValidator, message: StringValidator })
	static async postHelloWorld(req, res) {
		res.status(200).json({ message: req.body.message, times: req.body.repetition })
	}
}

export const postHelloWorld = functions.https.onRequest(HelloWorldApi.postHelloWorld)
```

This is all very cute with strings and integers, but what about arrays? Or discriminating unions? Or objects? With this sort of approach, it's actually extremely easy to do all of the above.

Let's address arrays first and foremost. An array validator just takes a pre-defined `BaseValidator` and applies it to every single element of an array:

```typescript
import { BaseValidator } from './baseValidator'

export class BaseArrayValidator extends BaseValidator {
    private readonly validator: BaseValidator

    constructor(validator: BaseValidator) {
        super()
        this.validator = validator
    }

    validate(input: any): boolean {
        return Array.isArray(input) && input.every(element => this.validator.validate(element))
    }

    parse(input: object): object {
        if (Array.isArray(input)) {
            return input.map(element => this.validator.parse(element))
        }
        throw new Error('non-array input given')
    }
} 
```

So now, to validate that an array is entirely composed of integers, we can just do the following:

```typescript
import { BaseArrayValidator } from './baseArrayValidator'
import { IntValidator } from './intValidator'

export const IntArrayValidator = new BaseArrayValidator(IntValidator)
```

Typescript also supports discriminating unions.

```typescript
type HelloWorldMessage = 'hello' | 'hi' | 'goodbye' | 'bye'
```

It would be nifty if our run-time validation could validate only a set of specific strings (or ints, or floats for that matter). In my opinion, it's almost always preferable to use an enum over discriminating unions, since enums boil down to javascript classes and can be preserved at run-time, while the latter are a typescript construct only.

```typescript
enum HelloWorldMessage {
	HELLO = 'hello',
	HI = 'hi',
	GOODBYE = 'goodbye',
	BYE = 'bye',
}
```

With that, we can develop a generic string Enum validator that can validate any string enum:

```typescript
import { BaseValidator } from './baseValidator'

export class StringEnumValidator extends BaseValidator {
    private readonly values: Set<string>

    // We cannot enforce a type `enum`, unfortunately due to javascript limitations
    // so we settle for `object` here
    constructor(input: object) {
        super()
        this.values = new Set(Object.keys(input))
    }

    validate(input: any): boolean {
        return typeof input === 'string' && this.values.has(input)
    }

    parse(input: string): string {
        return input
    }
}
```

You can imagine similar constructs can be developed for integers and floats. Lastly, objects. For objects, at Skip, we wanted to also support optional fields.


```typescript
import { BaseValidator } from './baseValidator'

// A generic valdiator that, given a map of key and validators,
// will validate a given object in the following ways:
// 1. the keys of the given object are all present in the validator map
// 2. each of the validator map's keys are in the object
// 3. all keys of the given object pass the validator stored in the map
// This also supports optional keys
export class ObjectValidator extends BaseValidator {
    private readonly validationMap: { [key: string]: BaseValidator }
    private readonly optionalMap: { [key: string]: BaseValidator }

    // For faster performance
    private readonly requiredKeys: string[]
    private readonly optionalKeys: string[]

    constructor(
        validatonMap: { [key: string]: BaseValidator },
        optionalMap: { [key: string]: BaseValidator } = {},
    ) {
        super()
        this.validationMap = validatonMap
        this.optionalMap = optionalMap
        this.requiredKeys = Object.keys(this.validationMap)
        this.optionalKeys = Object.keys(this.optionalMap)
    }

    validate(input: any): boolean {
        if (input === null || input === undefined) {
            return false
        }

        if (typeof input !== 'object') {
            return false
        }

        const inputKeys = Object.keys(input)
        const validKeys = this.requiredKeys.concat(this.optionalKeys)
        if (inputKeys.length > validKeys.length) {
            return false
        }

        // All required keys are present in the object and pass validation
        const inputKeySet = new Set(inputKeys)
        const requiredCheck = this.requiredKeys.every(key => inputKeySet.has(key) && this.validationMap[key].validate(input[key]))
        if (!requiredCheck) {
            return false
        }

        // All optional keys that are present pass validation (not every optional key must be present, however)
        const optionalCheck = this.optionalKeys.every(key => inputKeySet.has(key) ? this.optionalMap[key].validate(input[key]) : true)
        if (!optionalCheck) {
            return false
        }

        // All keys in the input object are either in optional or required maps - no unaccounted for key value pairs
        const validKeySet = new Set(validKeys)
        const allKeysAccountedFor = inputKeys.every(key => validKeySet.has(key))
        if (!allKeysAccountedFor) {
            return false
        }

        return true
    }

    parse(input: object): object {
        const inputKeys = Object.keys(input)
        const requiredKeySet = new Set(this.requiredKeys)
        const newObject = {}
        for (const k of inputKeys) {
            if (requiredKeySet.has(k)) {
                newObject[k] = this.validationMap[k].parse(input[k])
            } else {
                newObject[k] = this.optionalMap[k].parse(input[k])
            }
        }
        return newObject
    }
} 
```

Using this, we can actually re-write our post endpoint to take a single object, that looks more like the following:


```typescript
import functions = require('firebase-functions')

import { IntegerValidator } from '../parsers/integerValidator'
import { ObjectValidator } from '../parsers/objectValidator'
import { StringValidator } from '../parsers/stringValidator'

const helloWorldValidator = new ObjectValidator({
	message: StringValidator,
	repetition: IntValidator,
})

class HelloWorldApi {
	@post(helloWorldValidator)
	static async postHelloWorld(req, res) {
		res.status(200).json({ message: req.body.message, times: req.body.repetition })
	}
}

export const postHelloWorld = functions.https.onRequest(HelloWorldApi.postHelloWorld)
```

Of course, this would require a minor change to our `post` decorator, but you can see how with these changes we have established a contract for all of our endpoints to follow a specific json scheme that the backend can then enforce at runtime.

This was the product of roughly two days of thinking and playing around with type erasure, and it's a pretty cool testament to how extensible and powerful typescript really is! The [Typescript Playground](https://www.typescriptlang.org/play/) is also extremely useful in quickly prototyping and testing when unsure of the exact capabilities of a language.