<p align="center">
  <img src="https://i.imgur.com/SFOYzFb.png"/><br />
</p>

<h2 align="center">Musicorum Scheme 1.0 </h2>

> This scheme is still a draft

The Musicorum Scheme 1.0 defines the request rules for a standard communication.
#### Table of contents

1. [Workers](#workers)
    1. [Lookup](#lookup)
    2. [Generate](#generate)
2. [Content Manager](#content-manager)
3. [Gateway](#gateway)
    1. [Authorization](#gateway-auth)
    2. [Generate](#gateway-generate)
    3. [Workers](#gateway-workers)
    4. [Status](#gateway-status)


<h1 align="center" id="workers">Workers</h2>

<h2 id="lookup">Lookup</h3>

The lookup connection corresponds as the first connection from the gateway to the worker nodes to define the availability of the nodes and their available themes.

For each node, the gateway must make the following request and response:

*Note: workers are recommended to be local and not public to the world.*

`GET https://worker.musicorumapp.com/metadata`

```js
{
  "name": String, // Name of this particular worker
  "engine": String, // The generation engine used by this worker
  "version": Float, // The current version of the engine
  "scheme": Float, // The scheme version used
  "themes": String[] // An array of available themes
}
```

<details>
  <summary>Example</summary>

  ```json
  Content-Type: application/json; charset=UTF-8

  {
    "name": "rachel",
    "engine": "chloe",
    "version": 1.0,
    "scheme": 1.0,
    "themes": [
      "grid"
    ]
  }

  Response code: 200 (OK); Time: 16ms; Content length: 79 bytes
```
</details>

<br />
With the response, the gateway should map all the themes with their available workers.


<h2 id="generate">Generate</h3>
This is the method that the `gateway` will use to generate the image. The gateway must send all the data, as `ThemeData`, used to render the image, like texts, images, and everything done, without the worker needing to make other requests. After the generation, the worker must save the image on the export path, shared with all the other services.

> Note: Some themes does not require the user data, so it may return null for these cases. `hide_username` may also not be usable, but it's better to end `false` instead of `null`.
**Request**

`POST https://worker.musicorumapp.com/generate`

```js
{
  "id": String,
  "user": null | {
    "username": String,
    "name": String,
    "scrobbles": Integer,
    "image": String
  },
  "theme": String,
  "story": Boolean,
  "hide_username": Boolean,
  "data": ThemeData
}
```

**Response:**

```js
{
  "file": String,
  "duration": Number
}
```


<details>
  <summary>Example</summary>

  ```js
  POST https://worker.musicorumapp.com/generate
  Content-Type: application/json; charset=UTF-8

  {
    "id": "UMKp4hQJTVaEet2pWaHwUqGtBHKSogAyCdCvjqayTH94xj",
    "user": {
      "username": "metye",
      "name": "matheus",
      "scrobbles": 22193,
      "image": "https://i.scdn.co/image/ed0552e9746ed2bbf04ae4bcb5525700ca31522d""
    },
    "theme": "grid",
    "story": false,
    "hide_username": false,
    "data": {
      "tiles": [
        {
          "image": "https://i.scdn.co/image/ab67616d0000b2736040effba89b9b00a6f6743a",
          "name": "Replay",
          "secondary": "Lady Gaga"
        },
        {
          "image": "https://i.scdn.co/image/ab67616d0000b2733899712512f50a8d9e01e951",
          "name": "Play Date",
          "secondary": "Melanie Martinez"
        },
        ...
      ]
    }
  }
  
  >>>
  
  {
    "file": "UMKp4hQJTVaEet2pWaHwUqGtBHKSogAyCdCvjqayTH94xj.webp",
    "duration": 2.586826895
  }
```

</details>


<h1 align="center" id="content-manager">Content Manager</h2>

This section represents the [Content Manager](https://github.com/musicorum-app/content-manager), a micro service managed to cross-match and fetch infromation from external sources (such as Spotify or Deezer) using cache, in order to get data that Last.fm API does not provide or it would consume too many requests. This may be applicable for fetching artist images, [audio features](https://developer.spotify.com/documentation/web-api/reference/tracks/get-several-audio-features/), or anything else.


> This service is currently without documentation in order of future changes

<h1 align="center" id="gateway">Gateway</h2>

The gateway service is the proxy that redirects, queue, organize and controls all the other services. The service is also open to the public internet

<h2 id="gateway-auth">Authorization</h3>

Every request made to the Gateway needs to be authenticated and must have this header:

```http
Authorization: <API KEY>
```

<h2 id="gateway-generate">Generate</h3>

This represents the endpoint responsible to handle the image generation. It needs a Last.fm user(represented by `user`), a valid [theme](themes/)(represented by `theme`), a story option, and the `ThemeOptions`(represented by `options`), which is relative to the choosen theme, defined [here](themes/). There's also a `hide_username` boolean, that hides the username on the image if true.

**Request:**

```js
POST https://generator.musicorumapp.com/generate

{
  "user": String,
  "theme" String,
  "story": Boolean,
  "options": ThemeOptions,
  "language": String,
  "hide_username": Boolean
}
```

**Response:**

If success:
```js
{
  "success": true,
  "id": String,
  "worker": {
    "name": String,
    "engine": String,
    "version": Float,
    "scheme": Float,
  },
  "result": String
  "total_duration": Float,
  "generation_duration": Float
}
```
If error:
```js
{
  "success": false,
  "error": String,
  "message": String
}
```

<details>
  <summary>Example</summary>


  If success:
  ```js
  Content-Type: application/json; charset=UTF-8

  {
    "success": true,
    "id": "gOOLkrHAROqPUgDCCBUPq-AWRnN1cBw5Y0v3qriQ29Rf0w",
    "worker": {
      "name": "rachel",
      "engine": "chloe",
      "version": 1.0,
      "scheme": 1.0,
    },
    "result": "https://result.musicorumapp.com/gOOLkrHAROqPUgDCCBUPq-AWRnN1cBw5Y0v3qriQ29Rf0w.webp",
    "total_duration": 2.98003462,
    "generation_duration": 2.472550765
  }

  Response code: 200 (OK); Time: 1936ms; Content length: 748 bytes
  ```
  If error:
  ```js
  Content-Type: application/json; charset=UTF-8

  {
    "success": false,
    "error": "USER_NOT_FOUND",
    "message": "Last.fm user not found."
  }

  Response code: 404 (NOT FOUND); Time: 457ms; Content length: 356 bytes
  ```
</details>

<h2 id="gateway-workers">Workers</h3>

> WIP

<h2 id="gateway-status">Status</h3>

> WIP
