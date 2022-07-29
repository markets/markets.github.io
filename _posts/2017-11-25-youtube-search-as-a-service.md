---
layout: post
title:  "Suchtube: YouTube search as a service"
date:   2017-11-25
---

Let me introduce you `Suchtube`, Youtube search as a service.

SuchTube is a server and a CLI app to search videos on YouTube. It also comes with Slack integration, so you can search videos using a custom Slash Command:

```
/suchtube funny cats --random
```

The project is built with `Node.js`. You can found the source code (MIT licensed) and more documentation in GitHub: [https://github.com/markets/suchtube](https://github.com/markets/suchtube).

### CLI

The CLI allows you to search videos without leaving the terminal. Install globally it via:

```
> npm install -g suchtube
```

**NOTE** You we'll need a YouTube Data API key loaded in your ENV (`SUCHTUBE_YOUTUBE_DATA_API_V3`). You can get one easily by creating a project [here](https://console.developers.google.com/projectcreate).

Usage:

```
> suchtube funny cats
> suchtube football top goals --random --open
```

Or start the server:

```
> suchtube --server
```

### Suchtube as a library

You can also use `Suchtube` in your `Node.js` app:

```js
const suchtube = require('suchtube')

suchtube.search('funny cats', { random: true }).then(video => {
  console.log(video.title)
  console.log(video.link)
  console.log(video.publishedAt)
})
```

Hope you like it!
