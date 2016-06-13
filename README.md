
# Learn how to use *JSON Web Tokens* (JWT) for much *Authentication* win!

[![Build Status][travis-image]][travis-url]
[![Code Climate](https://codeclimate.com/github/dwyl/learn-json-web-tokens/badges/gpa.svg)](https://codeclimate.com/github/dwyl/learn-json-web-tokens)
[![Test Coverage](https://codeclimate.com/github/dwyl/learn-json-web-tokens/badges/coverage.svg)](https://codeclimate.com/github/dwyl/learn-json-web-tokens)
[![Dependency Status](https://david-dm.org/dwyl/learn-json-web-tokens.svg)](https://david-dm.org/dwyl/learn-json-web-tokens)
[![Node.js Version][node-version-image]][node-version-url]

# Learning JWT - Starting with Simple Session Management Approach for SPA/Hybrid Apps

The suggested pronunciation of JWT is the same as the English word
   "jot".
   ( https://tools.ietf.org/html/rfc7519 )




## *Why*?

The traditional ( tried and trusted ) approach to controlling authenticated
access to web app resources is to use session cookies and for many use
cases this continues to be the best approach. 

Using session cookies to provide authenticated access to resources is a mature approach
to typical login and access to protected services so why are so many developers turning
to using tokens and JWT as an alternative - especially in the context of SPA/Hybrid Apps?

### History - Why Alternatives to Session Cookies 

Some of the drivers that encouraged the evolution of an alternative approach include:
  - the rising prominence of API services and desire for an open auth standard for API access
  - the resistance to dependencies on browser implementation handling
  - the security concerns of sending the session cookie with every http request as relates to MITM attacks.
  - the increasing need to control access multiple micro-services from apps

These issues helped drive the implementation of OpenID, OAUTH, OAUTH 1.0 and OAUTH 2.0. 
These standards evolved and continue to experience controversy while back in the real world 
the large API providers adopt their own variations of these technologies and approaches filter
through to common use. 
One of the common components of these approaches is the use of tokens that encapsulate
information in various contexts through the auth communications 
( Auth, Bearer and Refresh tokens for example play different roles in coordinating the user access control ).


Do you want any (*all*) of these:

+ [x] Secure your website/app ***without cookies***
  + [x] No cookies means **no *annoying* cookie message** on your website
(see: [e-Privacy Directive](https://ico.org.uk/for-organisations/guide-to-pecr/cookies-and-similar-technologies/))  
note: don't worry, you can still *use* cookies in your app if you *really* want to!
*we've got you covered*: [dwyl/hapi-auth-jwt2#***store-your-jwt-in-a-cookie***](https://github.com/dwyl/hapi-auth-jwt2#want-to-sendstore-your-jwt-in-a-cookie)
+ [x] ***Stateless*** authentication (simplifies [***horizontal scaling***](http://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling))
+ [x] ***Minimise*** (some advantage) Cross-Site Request Forgery (**CSRF**) attacks.

## What?

> "***JSON Web Token*** *(JWT) is a compact* ***URL-safe*** *means of
> representing claims to be transferred between two parties.
> The claims in a JWT are* ***encoded*** *as a* ***JSON object*** *that is* ***digitally
> signed*** *using JSON Web Signature (JWS)*.  ~ IETF

###  In *English*

To identify/authenticate people in your (web/mobile) app,
put a ***standards-based token*** in the **header** or **url** of the page
(or API endpoint) which proves the user has logged in and is allowed to
access the desired content.

example: `https://www.yoursite.com/private-content/?token=eyJ0eXAiOiJKV1Qi.eyJrZXkiOi.eUiabuiKv`

**Note**: if this does not *look* "secure" to you,
scroll down to the "[***security***](https://github.com/dwyl/learn-json-web-tokens#q-if-i-put-the-jwt-in-the-url-or-header-is-it-secure)" section.


## Where JWT Excels in Real Life

  - Single use Access Tokens
  Imagine that you have wish to invite thousands of users to signup to access your application with an offer 
  and you have some details for each of these users such as their email or phone etc.
  
  One approach would be to create accounts for all of these users and associate an invite id and then
  send a url or link that includes details of this.
  or .. using JWT
    You could sign a jwt that includes their profile details ( email, phone ) and include this in a url.
    when the url is accessed you can verify the content of the JWT by performing a signing check and then
    use the details to create the account confident that the contents of the token have not been modified.
    
  - Simple multi micro service REST (stateless) API access

 - Transactional scoped access control.
   If you want the client to be able to perform a sequence of actions that can be completed in a commit step
   or totally cancelled within a timeframe then using JWT as a session management approach could be useful.



## Lazy Approach to OAUTH Type Access Control

With more developers consuming OAUTH API services there is growing familiarity with the JWT bearer tokens that
are provided through these services and when they come to developing their own REST APIS it
is understandable that they would want to align with these standards. 
Often the solution ends up using a single session token provided when a user logs in and
this looks fine. As things evolve though it can be very easy to end up with a convoluted solution
that is not fit for purpose because it suffers from the worst aspects of both session cookies
and an incomplete OAUTH. 

The Common JWT Approach to DIY Auth and Access Control including Session Management

### In Practice What Advantages Can We Expect to be Possible by starting to use JWT for access control
  - more control over access control token storage and no reliance on native browser implementation - On Android, this means SharedPreferences, on iOS, this means Keychain.
  - slightly less MITM attack vulnerability than session cookies because every request to the auth domain does not necessarily include the session cookie
  - greater flexibility in using the same token across domains when compared to cookies which are bound to a specific domain.
  - provides a path to a more robust OAUTH type aproach ( more short lived tokens to enhance security etc )
  - If the private signing key is shared across services you are able to confirm the identity of the user without
    the need to communicate with the authentication provider.
  - possible to validate the claim without performing a lookup of user session details
  - able to incorporate signed data as part of you access credential
    eg this could allow you to use the same credentials ( JWT ) to prove the identity of the user
    and also to describe authorization of use across different services. 
  - 
  

### In Practice What Kind of Issues Should I be Aware of
  - JWT tokens are not encrypted although they are signed. This means that anybody who has a copy of the token
    can examine any data stored within it. If there are sensitive details this can pose security risks.
  - may end up using session cookies and lose some of the advantages originally motivating the choice to use over session cookies
  - if you allow the token to be used as proof of identity to refresh tokens then you increase the security risks
    impact of an intercepted token.
  - as soon as you introduce a persistant storage state to the session you either lose the benefits of stateless
  sessions or introduce a layer of complexity ( flexibility ) to your architecture that requires a more sophisticated
  architectural understanding.
  - if using multiple services then this loose coupling can limit the control you have over session expiry etc.
  - JWT use can allow you to architect complex solutions but keep in mind that it is not an authentication/session
  solution but just a component - don't use this hammer for every auth problem nail.
  - if you start asking how to invalidate the token you are probably using them incorrectly. 
  You can either create an abstract layer beneath the token auth to handle this state. If you start trying to use 
  changing the signing key as a session termination approach you are probably doing the wrong thing.



### What does a JWT *Look* Like?

Tokens are a string of "url safe" characters which *encode* information.
Tokens have **three components** (separated by periods)
(shown here on multiple lines for *readability* but used as a single string of text)

```js
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9           // header
.eyJrZXkiOiJ2YWwiLCJpYXQiOjE0MjI2MDU0NDV9      // payload
.eUiabuiKv-8PYk2AkGY4Fb5KMZeorYBLw261JPQD5lM   // signature
```

#### 1. Header

The first part of a JWT is an encoded string representation
of a simple JavaScript object which describes the token along with the hashing algorithm used.

#### 2. Payload

The second part of the JWT forms the core of the token.
Payload length is proportional to the amount of data you store in the JWT.
General rule of thumb is: store the bare minimum in the JWT.


#### 3. Signature

The third, and final, part of the JWT is a signature generated
based on the header (part one) and the body (part two) and will be used
to *verify* that the JWT is valid.

### What are "Claims"?

Claims are the predefined **keys** and their **values**:

+ **iss**: issuer of the token
+ **exp**: the expiration timestamp (reject tokens which have expired). Note: as defined in the the spec, this must be in seconds.
+ **iat**: The time the JWT was issued. Can be used to determine the age of the JWT
+ **nbf**: "not before" is a future time when the token will become active.
+ **jti**: unique identifier for the JWT. Used to prevent the JWT from being re-used or replayed.
+ **sub**: subject of the token (rarely used)
+ **aud**: audience of the token (also rarely used)

See: http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html#RegisteredClaimName

# Example [![contributions welcome](https://img.shields.io/badge/contributions-welcome-brightgreen.svg?style=flat)](https://github.com/dwyl/learn-json-web-tokens/issues)

Lets get stuck in with a simple example.
(the *full* source is in the **/example** directory)

> TRY it: https://jwt.herokuapp.com/

## Server

Using the *core* **node.js http** server we create 4 endpoints in **/example/server.js**:

1. **/home** : home page (not essential but its where our **login** form is.)
2. **/auth** : *authenticate* the visitor (returns error + login form if failed)
3. **/private** : our restricted content - ***login required*** (valid session token) to see this page
4. **/logout** : invalidates the token and logs out the user (prevent from re-using old token)

We have *deliberately* made **server.js** as _simple as possible_ for:

+ Readability
+ Maintainability
+ Testability (all helper/handler methods are tested separately)

> note: if you can make it _simpler_, please submit an [issue](https://github.com/dwyl/learn-json-web-tokens/issues) to discuss!

## Helper Methods

All the helper methods are kept in **/example/lib/helpers.js**
The two most interesting/relevant methods are (simplified versions show here):

```javascript
// generate the JWT
function generateToken(req){
  var token = jwt.sign({
    auth:  'magic',
    agent: req.headers['user-agent'],
    exp:   Math.floor(new Date().getTime()/1000) + 7*24*60*60; // Note: in seconds!
  }, secret);  // secret is defined in the environment variable JWT_SECRET
  return token;
}
```
Which ***generates*** our JWT token when the user authenticates (this is then sent back to the client in the **Authorization** header for use in subsequent requests),

and

```javascript
// validate the token supplied in request header
function validate(req, res) {
  var token = req.headers.authorization;
  try {
    var decoded = jwt.verify(token, secret);
  } catch (e) {
    return authFail(res);
  }
  if(!decoded || decoded.auth !== 'magic') {
    return authFail(res);
  } else {
    return privado(res, token);
  }
}
```

Which **checks the JWT supplied by the client is valid**,
shows private ("privado") content to the requestor if valid
and renders the **authFail** ***error*** page if its not.

**Note**: *Yes*, *both* these methods are ***synchronous***.
But, given that neither of these methods require *any* **I/O** *or* **Network** requests,
its pretty safe to compute them synchronously.

> Tip: If you're looking for a ***Full Featured*** **JWT Auth Hapi.js plugin** (which does the verification/validation *asynchronously*) for your Hapi.js-based app please check out: [https://github.com/**dwyl/hapi-auth-jwt2**](https://github.com/dwyl/hapi-auth-jwt2)

## Tests

You may have noticed the [![Build Status][travis-image]][travis-url] badge at the *start* of this tutorial.
This is a sign the author(s) are not just *cobbling* code together.
The tests for both the server routes and helper functions are in: **/example/test**

1. /example/test/**functional.js** - *exercises* all the **helper methods** we created in /example/lib/**helpers.js**
[![Test Coverage](https://codeclimate.com/github/dwyl/learn-json-web-tokens/badges/coverage.svg)](https://codeclimate.com/github/dwyl/learn-json-web-tokens)
2. /example/test/**integration.js** - simulates the requests a *user* would send to the server and tests the *responses*.

Please *read* through the tests and *tell us* if anything is unclear!    
**Note**: We wrote a basic "***mock***" of the http req/res objects see: /example/test/**mock.js**
Confused/curious about Mocking? Read [When to Mock (by "Uncle Bob")](http://blog.8thlight.com/uncle-bob/2014/05/10/WhenToMock.html)

- - -

## Frequently Asked Questions (*FAQ*)

> ***Got a Question? Ask!*** >> https://github.com/dwyl/learn-json-web-tokens/issues


### Q: If I put the JWT in the *URL* or *Header* is it *secure*?

Good question! The *quick* **answer** is: ***No***.
Unless you are using SSL/TLS (http**s** in your url) to encrypt the connection,
sending the Token [***in-the-clear***](http://en.wikipedia.org/wiki/Plaintext)
is *always* going to be insecure (the token can be intercepted and re-used by a bad person...).    
A *naive* "*mitigation*" is to add *verifiable* "claims" to the token
such as checking that the request came from the ***same browser*** (user-agent),
**IP address** or more advanced
"[**browser fingerprints**](http://stackoverflow.com/a/3287761/1148249)"
... http://programmers.stackexchange.com/a/122385

The solution is to *either*:
+ use one-time-use (_single use_) tokens (_which expire after the link has been clicked_) ***or***
+ Don't use url-tokens where high degree of security is required.
(e.g: don't send someone a link which allows them to perform a transaction)

**Use-cases** for a JWT token in a url are:
+ account verification - when you email a person a link after they register on your site. `https://yoursite.co/account/verify?token=jwt.goes.here`
+ password re-set - ensures that the person re-setting the password has access to the email belonging to the account.
 `https://yoursite.co/account/reset-password?token=jwt.goes.here`

Both of these are good candidates for single-use tokens (_which expire after they have been clicked_).

### Q: How do we *Invalidate* sessions?

The person using your app has their **device** (phone/tablet/laptop)
***stolen***. How do you invalidate the token they were using?

The idea behind JWT is that the tokens are ***stateless***
they can be **computed** by any node in a cluster and verified
without a (slow) request to a database.

#### Store the Token in a Database?

##### LevelDB

If your app is *small* or you don't want to have to run a Redis server,
you can get most of the benefits of Redis by using LevelDB: http://leveldb.org/

We can ***either*** store the ***valid*** Tokens in the DB **or**
we can store the ***invalid*** tokens.
Both of these require a round-trip to the DB to check if valid/invalid.
So we prefer to store ***all*** tokens and update the
**valid** property of t from true to false

Example record stored in LevelDB
```json
"GUID" : {
  "auth" : "true",
  "created" : "timestamp",
  "uid" : "1234"
}
```
We would lookup this record by its GUID:

```js
var db = require('level');
db.get(GUID, function(err, record){
  // pseudo-code
  if(record.auth){
    // display private content
  } else {
    // show error message
  }
});
```
see: example/lib/helpers.js **validate** method for detail.

##### Redis

Redis is the *scalable* way of storing your tokens.

If you are *totally* new to Redis read:
+ Intro: http://redis.io/topics/introduction
+ Redis in 30 mins:
http://openmymind.net/2011/11/8/Redis-Zero-To-Master-In-30-Minutes-Part-1/
+ What is Redis? http://www.slideshare.net/dvirsky/introduction-to-redis

Redis ***Scales*** (provided you have the RAM):
http://stackoverflow.com/questions/10478794/more-than-4-billion-key-value-pairs-in-redis

> ***Get Started with Redis today***! [https://github.com/dwyl/**learn-redis**](https://github.com/dwyl/learn-redis)

#### Memcache?

***Quick* answer**: *use **Redis***:
http://stackoverflow.com/questions/10558465/memcache-vs-redis


### Q: Returning Visitor (*no State Preservation between sessions*)

Cookies are stored on the client and are sent by the browser to the server on every request. If the person *closes* their browser, cookies are preserved, so they can continue where they left off without having to log-in again. However, cookies will be sent on **all** requests that match the path and issuing domain, including those for images and css, where it isn't needed.

[`localStorage`](https://developer.mozilla.org/en-US/docs/Web/API/Window.localStorage) provides a better mechanism for storing tokens during and between browser sessions.

#### Browser-based Applications

There are two options for storing your JWTs:
1. Use ***localStorage*** to store your JWTs on the client side (_means you need to remember to send the JWT in your `authorization` header for subsequent http/ajax requests_)
2. Store your JWT in a cookie (_set and forget_)

> We _obviously_ prefer the cookie-less approach.
But if done right, cookies still have their place in modern web apps!
(_see the Auth0 article on "10 things you should know" in the further reading below_)

##### Useful Links

+ Good ***history*** & overview of **Localstorage**:
http://diveintohtml5.info/storage.html
+ MDN **Window.localStorage**:
https://developer.mozilla.org/en-US/docs/Web/API/Window.localStorage
+ Brief description + basic *examples*:
http://www.html5rocks.com/en/features/storage
+ Will it work for *my* visitors?
http://caniuse.com/#search=localstorage
(**Quick answer**: ***Yes***! IE 8 & above, Android 4.0+, IOS 7.1+, Chrome & Firefox )


#### Programatic (API) Access

Other services accessing your API will have to store the token in a
retrieval system (e.g: Redis or SQLite for mobile apps) and send the token back on each request.

### How to generate secret key?

> "*Apologies if this is mentioned elsewhere. The private key used for signing the tokens, is this the same as a private key generated using ssh-keygen?*" ~ Originally asked by [@skota](https://github.com/skota) see: [dwyl/**hapi-auth-jwt2/issues**/48](https://github.com/dwyl/hapi-auth-jwt2/issues/48)


Since JSON Web Tokens (JWT) are not signed using [***asymmetric encryption***](http://en.wikipedia.org/wiki/Public-key_cryptography) you do not *have* to generate your secret key using ***ssh-keygen***. You can just as easily use a ***strong password*** e.g: https://www.grc.com/passwords.htm provided its ***long and random***. The chance of collision (and thus someone being able to decode your encoded JSON) is pretty low. And if you join two of those **Strong Passwords** (*strings*) together, you'll have a 128bit ASCII String. So the chances of collision are less than the [number of *atoms* in the universe](http://en.wikipedia.org/wiki/Observable_universe#Matter_content_.E2.80.94_number_of_atoms).

To quickly and easily create a secret key using Node's crypto library, run this command.

    node -e "console.log(require('crypto').randomBytes(32).toString('hex'));"

In other words, you *can* use an ***RSA key***, but you don't *have to*.

The main thing you need to remember is: don't share the key with people who are not in your core ("*DevOps Team*") or *accidentally* publish it by committing it to GitHub!




## Which Node.js Module?

A search for "**JSON Web Token**" on NPM:
https://www.npmjs.com/search?q=json+web+token yields ***many*** results!

![npm search for json web token](http://i.imgur.com/ZLN3LlW.png)

### Building a Web App with Hapi.js?

In our efforts to simplify using JWTs in Hapi.js apps,
we wrote this module: https://github.com/dwyl/hapi-auth-jwt2


### General Use in *Other* Node.js Projects

We *highly* recommend using the **jsonwebtoken** module
made by our friends [@auth0](https://twitter.com/auth0)
([the identity/authentication experts](https://auth0.com/about)):
- https://github.com/auth0/node-jsonwebtoken
Which in turn uses:
https://github.com/brianloveswords/node-jws
[![NPM][jsonwebtoken-icon] ][jsonwebtoken-url]

Another great option is: https://github.com/joaquimserafim/json-web-token
by our friend [@joaquimserafim](https://github.com/joaquimserafim)

## Essential Reading (_Background_)

- Original **Specification** (Draft):
https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
- Great overview from Atlassian:
https://developer.atlassian.com/static/connect/docs/latest/concepts/understanding-jwt.html
- Good intro (ruby-specific examples):
http://www.intridea.com/blog/2013/11/7/json-web-token-the-useful-little-standard-you-haven-t-heard-about
+ Friendlier introduction: http://jwt.io/
+ Getting to know JWT:
https://scotch.io/tutorials/the-anatomy-of-a-json-web-token
- Discussion: https://ask.auth0.com/c/jwt
+ ***How to*** do **stateless authentication** (session-less & cookie-less):
http://stackoverflow.com/questions/20588467/how-to-do-stateless-session-less-cookie-less-authentication


## Further Reading (_Recommended_) [![contributions welcome](https://img.shields.io/badge/contributions-welcome-brightgreen.svg?style=flat)](https://github.com/dwyl/learn-json-web-tokens/issues)

+ JWT with Passport.js:
http://stackoverflow.com/questions/20228572/passport-local-with-node-jwt-simple
+ JWT Tokens as API Keys:
https://auth0.com/blog/2014/12/02/using-json-web-tokens-as-api-keys/
+ **10 Thinks you should know** about ***Tokens and Cookies***:
https://auth0.com/blog/2014/01/27/ten-things-you-should-know-about-tokens-and-cookies/#xss-xsrf
+ Information Security discussion:
http://security.stackexchange.com/questions/51294/json-web-tokens-jwt-as-user-identification-and-authentication-tokens
+ Using JWT with node.js (express + backbone):
http://www.sitepoint.com/using-json-web-tokens-node-js/
+ Token-based Authentication with Socket.IO
https://auth0.com/blog/2014/01/15/auth-with-socket-io/
+ JWT Auth *discussion* on Hacker News:
https://news.ycombinator.com/item?id=7084435
+ The Spec but nicer:
http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html
+ Extended (Wiki) article on Claims-based authentication:
http://en.wikipedia.org/wiki/Claims-based_identity
+ Securing Requests with JWT:
http://websec.io/2014/08/04/Securing-Requests-with-JWT.html
+ Avoid Database in authenticating user for each request (stateless):
http://security.stackexchange.com/questions/49145/avoid-hitting-db-to-authenticate-a-user-on-every-request-in-stateless-web-app-ar
+ The Twelve-Factor App: http://12factor.net/ + http://12factor.net/processes
+ Auth in Hapi with JWT: https://medium.com/@thedon/auth-in-hapi-with-jwt-780ce4d072c7#.clgj5lknq

# *Thanks* for Learning with Us!

If you found this quick guide useful, please star it on GitHub!
and re-tweet to share it with others: https://twitter.com/olizilla/status/626487231860080640

[![olizilla tweet](http://i.imgur.com/rCvNvvk.jpg)](https://twitter.com/olizilla/status/626487231860080640 "Please Re-Tweet!")


[jsonwebtoken-icon]: https://nodei.co/npm/jsonwebtoken.png?downloads=true
[jsonwebtoken-url]: https://npmjs.org/package/jsonwebtoken
[npm-image]: https://img.shields.io/npm/v/esta.svg?style=flat
[npm-url]: https://npmjs.org/package/esta
[node-version-image]: https://img.shields.io/node/v/esta.svg?style=flat
[node-version-url]: http://nodejs.org/download/
[downloads-image]: https://img.shields.io/npm/dm/esta.svg?style=flat
[downloads-url]: https://npmjs.org/package/esta
[travis-image]: https://img.shields.io/travis/dwyl/learn-json-web-tokens.svg?style=flat
[travis-url]: https://travis-ci.org/dwyl/learn-json-web-tokens
[coveralls-image]: https://img.shields.io/coveralls/dwyl/learn-json-web-tokens.svg?style=flat
[coveralls-url]: https://coveralls.io/r/dwyl/learn-json-web-tokens?branch=master
[dependencies-url]: https://david-dm.org/dwyl/learn-json-web-tokens
[dependencies-image]: https://david-dm.org/dwyl/learn-json-web-tokens.svg
