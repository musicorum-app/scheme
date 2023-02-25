# Grid theme

## Definitions 

```ts
interface GridTile {
  image: string
  name: string
  secondary: string | null
  playcount: number | null
}

type GridStyle =
  "DEFAULT" |
  "CAPTION" |
  "SHADOW"

type Period =
  "7DAY" |
  "1MONTH" |
  "3MONTH" |
  "6MONTH" |
  "12MONTH" |
  "OVERALL"

type Entity =
  "ARTIST" |
  "ALBUM" |
  "TRACK"
```

## Worker data
This is the data that must be sent to the worker as `data`.

```ts
interface GridThemePayload {
  tiles: GridTile[]
  padded: boolean // padding between tiles
  rows: number
  columns: number
  show_names: boolean
  show_playcount: boolean
  style: GridStyle
  tile_size: number // tile size (width and height)
}
```

## Collage data
This is the data that must be sent to the api as `options`.

```ts
interface GridThemePayload {
  rows: number // between 3 - 20
  columns: number // between 3 - 20
  show_names: boolean
  show_playcount: boolean
  padded: boolean
  period: Period
  entity: Entity
  style: GridStyle
}
```