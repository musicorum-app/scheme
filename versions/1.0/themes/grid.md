# Grid theme

## Definitions

```ts
GridTile {
  "image": String,
  "name": String,
  "secondary": String?,
  "scrobbles": Number?
}

enum GridStyle [
  "DEFAULT",
  "CAPTION",
  "SHADOW"
]

enum Period [
  "7DAY",
  "1MONTH",
  "3MONTH",
  "6MONTH",
  "12MONTH",
  "OVERALL"
]

enum GridType [
  "ARTIST",
  "ALBUM",
  "TRACK"
]
```

## Worker data
This is the data that must be sent to the worker as `data`.

```ts
GridThemePayload {
  "tiles": GridTile[]
  "rows": Integer, // between 3 - 20
  "columns": Integer, // between 3 - 20
  "show_names": Boolean,
  "show_playcount": Boolean,
  "style": Enum(GridStyle),
  "tile_size": Integer
}
```

## Generator data
This is the data that must be sent to the generate gateway as `options`.

```ts
GridThemePayload {
  "rows": Number, // between 3 - 20
  "columns": Number, // between 3 - 20
  "show_names": Boolean,
  "show_playcount": Boolean,
  "period": Number[] | Enum(Period) // If array of number, they represent a period of two timestamps
  "type": Enum(GridType),
  "style": Enum(GridStyle)
}
```


## Grid styles
![GridStyles](https://i.imgur.com/B5mBxrB.jpg)
