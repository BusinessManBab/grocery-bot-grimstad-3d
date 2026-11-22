# Grocery Bot 3D map preview: Grimstad

Grimstad, Norway, in 2D and 3D on the town's real terrain. Built from
[grocery-bot-3d-hills-preview](https://github.com/BusinessManBab/grocery-bot-3d-hills-preview) by swapping in
Grimstad's town data, following [OTHER-CITIES.md](OTHER-CITIES.md).

- Real terrain: the 3D map is draped over an elevation grid of Grimstad (`data/terrain.bin`, 349 × 493 samples every ~9 m, 0–96 m, relief ×1.6). Roads, buildings, trees, shops, trucks (pitched on slopes) and trails follow the ground; the board edge shows the hills in section, and the sea stays flat.
- Cartoon buildings from their OSM type, with pitched roofs and chimneys on houses, balconies on apartment blocks, shop windows, corrugated industrial walls, garage doors and the church spire. Order homes keep their own materials, so claiming players' colours still show.
- Forests: spruce and birch on OSM woodland and on empty land away from town (`data/landcover.json`), with green painted under every tree on the 2D map and the 3D ground. Fields, built-up land, parks, roads, water and shop plazas stay clear.
- Shop window (bottom right): the v1 grocerybot.no store animation (Erik's `arena.js`, ported to `src/shop-iso.js`), laid out like the real store — **Dairy** fridges, **Pantry** shelves, **Produce** bins and **Bakery** racks as double rows from the back wall, with department signs. Its trucks are each player's own map trucks, with their cab and carriage shapes, colours and decals. Clicking a shop on the map or a player in the standings switches the window; the order card (left) opens when you click an order house.
- Bridges: roads cross water on raised decks with railings and piers; trucks drive over them.
- The river flows: streaks drift downstream along each river (direction from a distance-to-outlet field over the water areas), faster where the river drops. The Groosefjorden is still.


## Landmarks

Three of Grimstad's trademarks are modelled by hand in `src/landmarks.js` from `tools/landmarks.spec.json`
(baked by `node tools/bake_landmarks.mjs` into `data/landmarks.json` and the 3D **Explore** menu, `data/places.json`):

- **Grimstad kirke** (1881, Henrik Thrap-Meyer): one of Norway's largest wooden churches, a neo-Gothic cross church
  on its real OSM outline, painted light green with yellow-ochre trim (its original colours), pointed windows,
  slate roofs and the west tower with a tall eight-sided slate spire. Heights (9 m walls, 23 m tower, 40 m spire)
  are estimated from photos.
- **Ibsenhuset** (Ibsen-museet): the old pharmacy where Henrik Ibsen was an apprentice and wrote *Catilina*, a
  white-panelled gable house from 1757 with small-paned windows, on its OSM outline.
- **Grimstad havn**: the guest harbour's wooden jetties on piles along the OSM pier lines by Dampskipsbrygga and
  Terje Vigens brygge, with white motor boats and sailing boats moored along them.

All of it costs 4 draw calls and a few thousand triangles. Sources: Wikipedia (Grimstad Church, Ibsen-museet i
Grimstad), lokalhistoriewiki (Østre Strandgade 13), Wikimedia Commons photos, OpenStreetMap.

## Loading

The Oslo map's fast-load strategy: `tools/bake_buildings.mjs` packs the buildings into `data/buildings.bin`
(6×6 chunks, simplified footprints, shared walls kept on one side only) and `data/homes.json`;
`tools/bake_trees.cjs` bakes the forest into `data/trees.bin`. In the page, module workers build the chunk
meshes nearest-first (`src/city-stream.js`, `src/world-worker.js`), three.js loads minified with a modulepreload,
and phones get a lighter quality tier (`?quality=low|high`). The 3D view is on screen in about 2 s at 60 fps on
the test GPU (before: about 6 s and 38 fps). Flicker fixes: doors stand off the walls, neighbouring roofs sit a
few cm apart and the camera's near plane follows its height.

The data came from the `town/grimstad` branch of `astar-as/grocery-bot` (10 shops, 85 delivery addresses,
10 map labels) and OpenStreetMap. 84 of the 85 delivery homes matched a real building footprint. The match is
a simulated 600-tick game on the Grimstad map with the realistic shop layout.

To make this for another town, follow [OTHER-CITIES.md](OTHER-CITIES.md) (data scripts in `tools/build_city.py`).
`tools/simulate_match.py` takes an optional town id as its fifth argument.

Switch views with the 2D / 3D buttons (or V, or `?view=3d`).

Elevation: Mapzen Terrarium tiles (AWS Open Data). Map data © OpenStreetMap contributors (ODbL).
