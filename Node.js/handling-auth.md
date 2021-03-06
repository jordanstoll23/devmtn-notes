## Todd's Focus Questions:
- _What does Massive allow us to do?_
- _How are Querys different from URL Parameters?_
- _When should we use Querys over URL Paramets?_
- _When does middleware run?_

## Using Massive to Authenticate users
### When do we want to authenticate our users?
   - If we want to create a unique experience for our users using their personal info
   - If our server needs to know exactly who is accessing information

### What are we using to authenticate them?
- auth0 as a client for hosting the login
  - allows us to permit Google, Facebook, Github, & other outside login sync
- JS Library `passport` --> Authentication middleware for Node
- HTTP Requests get us started 
- `Form-Based AUTH` - HTML form where you enter credentials
    - This info is then sent to a server 
    - Seen all over websites 

- Encryption - transforms data with the goal of keeping the info secret 
    - `text` => `encryption algorithm` + `key` => `ciphertext` (something that's been encrypted)
    - Hashed Passwords - random strings of characters. A step beyond encryption
        - Server stores the hashed password, not original 
        - Same input, same algorithm, same hash result (which is then stored)
        - 'Salting' - adds a random string to the end of a hashed password 

- Third parties can host authentication servers - "Login with Google, Facebook, Github"

- Sessions - allow us to /stay/ logged in once we've been authenticated 
    - `req.session.user` - logs information to that individual session (SID)
    - `user_id` - what we use to confirm whether or not this user already has an account on our db (when using Auth0)

## Step by Step
#### Getting started:
- `npm i --save express body-parser express-session passport passport-auth0`
    - `massive`: if we were connecting to the database
    - `axios`: if we were saving the user data to our db
    - create server folder in the root > `server.js` 
      ```
        const express = require('express');
            bodyParser = require('body-parser');
            session = require('express-session');
      ```
      ```
        const app = express();
        app.use( bodyParser.json() );
        app.use( session({
            secret: 's3cr3t',
            resave: false,
            saveUninitialized: true
        }));
      ```
      ```
        const PORT = xxxx; 
      ```
    - make this different than your frontend port, i.e. `3030`
      ```
        app.listen( PORT, ()=> console.log("Docked at port", PORT) );
      ```

    - now utilize new packages
    - `passport` and `passport-auth0`
      ```
        app.use( passport.initialize() );
        app.use( passport.session() );
      ```
      ```
        passport.use(new Auth0Strategy({
            domain: "this-domain.auth0.com",
            clientID: "random23Gibberish",
            clientSecret: "more45gibberish67and90strings",
            callbackURL: "makesurethismatches.yourauth0.settings"
        }, function(accessToken, refreshToken, extraParams, profile, done) {
            done(null, profile)
        }));
      ```

    - create some endpoints 
      ```
        app.get('/auth', passport.authenticate('auth0'));
        app.get('/auth/callback', passport.authenticate('auth0', {
            successRedirect: 'http://localhost:3000/',
            failureRedirect: 'http://localhost:3000/'
        }));
      ```

    - required for `passport-auth0`
      ```
        passport.serializeUser(function(user, done) {
            done(null, user);
        });
      ```
      ```
        passport.deserializeUser(function(obj, done) {
            done(null, user);
        });
      ```

    - create a `.env` file in the root
    - `npm i dotenv`
    - and require it!
      ```
        require('dotenv').config();
      ```

    - put the following designations in your > `.env` file
      ```
        SECRET=
        AUTH_DOMAIN=kale-stew.auth0.com
        AUTH_CLIENT_ID=
        AUTH_CLIENT_SECRET=
        AUTH_CALLBACK=
      ```

    - move back into `server.js` and replace designated values in your code **before** pushing to git !
      ```
        secret: process.env.SECRET
        domain: process.env.AUTH_DOMAIN
        clientID: process.env.AUTH_CLIENT_ID
        clientSecret: process.env.AUTH_CLIENT_SECRET
        callbackURL: process.env.AUTH_CALLBACK
      ```