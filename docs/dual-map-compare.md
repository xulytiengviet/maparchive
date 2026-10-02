# Then / Now — historical vs current WebGIS

This fork turns the default `/explore` experience into a synchronized two-map
comparison.

## Layout

- **Left — Bản đồ xưa / Historical:** the top georeferenced historical sheet
  from the existing layer stack.
- **Right — Hiện nay / Current:** a modern basemap with its own Streets /
  Satellite switch.
- Both maps share the **same OpenLayers View object**, so pan, zoom and rotation
  are identical. There is no event-to-event camera relay and therefore no drift.
- A centre crosshair is rendered on both panes to make street, river and parcel
  alignment easier to inspect.
- The existing **Stacked** and **Lens** modes remain available; **Then / Now** is
  now the default for `/explore`.

## Data and licensing

The software remains under the repository's MIT licence. Historical
georeferences, reviewed labels, gazetteer records and traced features from the
Vietnam Map Archive Project retain their CC BY 4.0 attribution requirements.
Scanned map images keep the terms of their holding institution and are not
relicensed by this fork. OpenStreetMap-derived basemap data remains subject to
ODbL attribution, while Esri satellite imagery remains subject to Esri terms.

See `NOTICE.md` before redistributing imagery or derived datasets.
