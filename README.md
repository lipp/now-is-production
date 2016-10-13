# The problem

Depending if your deployment is staging or production, your service might behave differently.

When using [now](https://zeit.co/now), your deployment gets a "random" url when deployed initially. 
If you want your deployment to go production, a `now alias` is created.
After re-eploying your service, the old deployment may have to do some cleanup or shutdown (e.g. closing socket connections).

# The solution

This module periodically polls if the running deployment (NOW_URL env) has an alias to the production url.

```js
const nowIsProduction = require('now-is-production')
const now = nowClient('YOUR TOKEN')

const listener = nowIsProduction({
  api: now,
  productionUrl: 'live.yourservice.com',
  checkInterval: 30000 // 30secs
})

listener.on('staging', (wasProduction) => {
  if (wasProduction) {
    // close real DB connection
    // process.exit(0)
  } else {
    // connect dummy DB
  }
})

listener.on('production', (wasStaging) => {
  if (wasStaging) {
    // close dummy DB
  }
  // connect real DB
})
```
