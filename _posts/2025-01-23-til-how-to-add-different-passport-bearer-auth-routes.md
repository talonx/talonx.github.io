---           
layout: post
title: Today I Learned - How to Add Different Passport Bearer Auth Methods for Different Routes
date: 2025-01-23 00:00:00 IST
comments: true
categories: today-I-learned
---

I have a route in ExpressJS that is protected by Passport bearer auth. The docs have a straightforward example which works if that is the only strategy you need.

```javascript
passport.use(new Strategy(
    function (token: any, cb: any) {
        validateToken(token).then(userid => {
            if (userid) {
                return cb(null, userid)
            } else {
                return cb(null, false)
            }
        }).catch(error => {
            console.log("[EXT_ROUTES]Failed bearer auth", error);
            return cb(error);
        })
    }
));
```
which is used later like this:
```javascript
app.use('/api/v1/ext',
    passport.authenticate('bearer', { session: false }),
    externalRoutes,
)
```

Now I need to add a route that is protected by a different bearer auth strategy. The [official docs](https://www.passportjs.org/packages/passport-http-bearer/) don't have clarity on this.

It turns out that the string 'bearer' in the passport.authenticate call is an identifier for the strategy. Defining a new strategy then becomes:
```javascript
passport.use("cached-bearer", new Strategy(
    function (token, cb) {
        validateCachedToken(token).then(userid => {
            if (userid) {
                return cb(null, userid);
            } else {
                return cb(null, false);
            }
        }).catch(error => {
            console.log("[EXT_ROUTES]Failed cached bearer auth", error);
            return cb(error);
        })
    }
));
```
and it can be used as:
```javascript
app.use('/api/v1/inbound',
    passport.authenticate('cached-bearer', { session: false }),
    inboundRoutes,
);
```

This [Stackoverflow answer](https://stackoverflow.com/a/57047241) pointed me towards the solution.

It's curious as to how a lot of official docs don't handle anything beyond the simplest cases, and also don't explain basics. The docs are the first thing I look at when doing something with a new library, and it's often a struggle
when they are not sufficient.