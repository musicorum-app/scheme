# Grid theme

## Definitions

```ts
GridTile {
  "image": String,
  "name": String,
  "secondary": String?,
  "scrobbles": Number
}

GridStyle [
  DEFAULT,
  CAPTION,
  SHADOW
]

Period [
  7DAYS,
  1MONTH,
  3MONTHS,
  6MONTHS,
  1YEAR,
  OVERALL
]
```

## Worker data

```ts
GridThemePayload {
  "tiles": GridTile[]
  "rows": Number, // between 3 - 20
  "columns": Number, // between 3 - 20
  "show_names": Boolean,
  "show_playcount": Boolean,
  "style": Enum(GridStyle),
  "tile_size": Number
}
```

## Generator data

```ts
GridThemePayload {
  "tiles": GridTile[]
  "rows": Number, // between 3 - 20
  "columns": Number, // between 3 - 20
  "show_names": Boolean,
  "show_playcount": Boolean,
  "period": Number[] | Enum(Period) // If array of number, they represent a period of two timestamps
  "style": Enum(GridStyle)
}
```


## Grid styles
![GridStyles](https://i.imgur.com/B5mBxrB.jpg)
