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
2. [Resource Manager](#resource-manager)
    1. [Fetch Spotify Artists](#spotify-artists)
3. [Gateway](#gateway)
    1. [Authorization](#gateway-auth)
    2. [Generate](#gateway-generate)
    3. [Workers](#gateway-workers)
    4. [Status](#gateway-status)


<h2 align="center" id="workers">Workers</h2>

<h3 id="lookup">Lookup</h3>

The lookup connection corresponds as the first connection from the gateway to the worker nodes to define the availability of the nodes and their available themes.

For each node, the gateway must make the following request and response:
`GET http://worker.node/worker`

```js
{
  "name": String,
  "engine": String,
  "version": Float,
  "scheme": Float,
  "themes": String[]
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

<h3 id="generate">Generate</h3>

> WIP


<h2 align="center" id="resource-manager">Resource Manager</h2>

This section represents the [Resource Manager](https://github.com/musicorum-app/resource-manager), a micro service managed to cross-match and fetch infromation from external sources (such as Spotify or Deezer), in order to get data that Last.fm API does not provide or it would consume too many requests. This may be applicable for fetching artist images, [audio features](https://developer.spotify.com/documentation/web-api/reference/tracks/get-several-audio-features/), or anything else.
<h3 id="spotify-artists">Fetch Artists</h3>

This request takes a list of artist names and returns a `ArtistResponse` or `null`.

**Request:**
```js
POST http://resource.manager/fetch/artists

{
  "artists": String[]
}
```
<details>
  <summary>Example</summary>

  ```json
  POST http://resource.manager/fetch/artists
  Content-Type: application/json; charset=UTF-8

  {
    "artists": [
      "Kali uchis",
      "Lady gaga",
      "Frank Ocean",
      "An artist name that hopefully does not exist"
    ]
  }
```
</details>
<br />


**Response:**
```js
[
  {
    "name": String,
    "hash": String,
    "url": String,
    "spotify": String
  } | null
]
```

<details>
  <summary>Example</summary>

  ```json
  Content-Type: application/json; charset=UTF-8

  [
    {
        "name": "Kali Uchis",
        "hash": "63f68448a640d2691b311e80dfd67891f46550dc",
        "url": "https://i.scdn.co/image/1617d6c0e35dffb12eeec77815d61af71f865ad9",
        "spotify": "1U1el3k54VvEUzo3ybLPlM"
    },
    {
        "name": "Lady Gaga",
        "hash": "8383761a48b26a08cc5f31207940640720bfca90",
        "url": "https://i.scdn.co/image/22e5bd06cd56f4bc430e0d4eb95af75565fdffa8",
        "spotify": "1HY2Jd0NmPuamShAr6KMms"
    },
    {
        "name": "Frank Ocean",
        "hash": "4e90768c3b23d1168429a1635995f41be44cb37f",
        "url": "https://i.scdn.co/image/0cc22250c0b18183e5c62f240a5756ec5226dee1",
        "spotify": "2h93pZq0e7k5yf4dywlkpM"
    },
    null
]

  Response code: 200 (OK); Time: 534ms; Content length: 669 bytes
```
</details>

<h2 align="center" id="gateway">Gateway</h2>

The gateway service is the proxy that redirects, queue, organize and controls all the other services. The service is also open to the public internet

<h3 id="gateway-auth">Authorization</h3>

Every request made to the Gateway needs to be authenticated and must have this header:

```http
Authorization: <API KEY>
```

<h3 id="gateway-generate">Generate</h3>

This represents the endpoint responsible to handle the image generation. It needs a Last.fm user(represented by `user`), a valid [theme](themes/)(represented by `theme`), a story option, and the `ThemeOptions`(represented by `options`), which is relative to the choosen theme, defined [here](themes/).

**Request:**

```js
POST http://gateway.node/generate

{
  "user": String,
  "theme" String,
  "story": Boolean,
  "options": ThemeOptions
}
```

**Response:**

If success:
```js
{
  "success": true,
  "hash": String,
  "id": String,
  "worker": {
    "name": String,
    "engine": String,
    "version": Float,
    "scheme": Float,
  },
  "result": String
  "duration": Integer,
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
    "hash": "9b17780f9fa65fe2e886c72dbf91c62588e6324a8076b320378c164d79c3753e",
    "id": "j3QTU7DAvrrmE2SX9d3uBbnxfr7nuOprZPf3vdDjsJSNfK",
    "worker": {
      "name": "rachel",
      "engine": "chloe",
      "version": 1.0,
      "scheme": 1.0,
    },
    "result": "https://result.musicorumapp.com/j3QTU7DAvrrmE2SX9d3uBbnxfr7nuOprZPf3vdDjsJSNfK.jpg",
    "duration": 1827,
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

<h3 id="gateway-workers">Workers</h3>

> WIP

<h3 id="gateway-status">Status</h3>

> WIP