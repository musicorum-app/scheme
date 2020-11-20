# Grid theme

## Data

```ts
GridThemePayload {
  "tiles": GridTile[]
  "rows": Number, // between 3 - 20
  "columns": Number, // between 3 - 20
  "show_names": Boolean,
  "show_playcount": Boolean,
  "style": Enum(GridStyle)
}

GridTile {
  "image": String,
  "name": String,
  "secondary": String?
}

GridStyle [
  DEFAULT,
  CAPTION,
  SHADOW
]
```


## Grid styles
![GridStyles](https://i.imgur.com/B5mBxrB.jpg)