FOE HELPER 4.8.7.0 — DEVELOPMENT COPY WITH PLACED BUILDINGS

This archive is built from the exact 4.8.7.0 source folder uploaded for this conversation.
Your store-installed FoE Helper is not changed by using this development copy.

INSTALLATION
1. Extract this ZIP to a permanent folder, for example:
   Documents\FoE Helper Development\
2. Open Opera GX and navigate to opera://extensions.
3. Enable Developer mode.
4. Turn OFF the original store-installed FoE Helper toggle. Do not remove it.
   Both versions inject scripts into the same Forge of Empires page; running both at once can cause collisions.
5. Click “Load unpacked”. Select the extracted folder that directly contains manifest.json.
6. Open/refresh Forge of Empires. The extension should appear in the extensions list as
   “FoE Helper Development - Placed Buildings”.
7. Open the FoE Helper menu and select “Placed Buildings”.

TO RETURN TO THE ORIGINAL
1. Turn OFF “FoE Helper Development - Placed Buildings” in opera://extensions.
2. Turn ON the original FoE Helper store installation.
3. Refresh the Forge of Empires game tab.
Do not delete the original extension or its original profile folder.

WHAT CHANGED IN THIS COPY
- Added js/web/placedBuildings/js/placedBuildings.js
- Added js/web/placedBuildings/css/placedBuildings.css
- Added the module name “placedBuildings” to js/internal.json.
- Added the menu item, tooltip and click handler to js/web/_menu/js/_menu.js.
- Removed only the manifest “key” field so Opera assigns this unpacked copy a distinct local ID.
  The original store-installed ID is not changed. The update_url field is retained so FoE Helper's
  own devMode detection does not automatically enable beta functions.
- Changed the displayed extension name to distinguish the development copy.

FEATURE SCOPE
- Lists recognised entities in MainParser.CityMapData whose calculated city footprint is greater than zero.
- Includes streets and other entities with a tile footprint; excludes known off-grid/outpost-only types.
- Supports search, multi-select age and type filters, production-property filters with Any/All logic,
  sorting, grouping by age/type, footprint totals, population, happiness and efficiency score.
- Uses the 4.8.7.0 CityMap.GetBuildingSize, CityBuildings.createBuilding and Productions routines.
- The Locate action uses BuildingMarker.show().
- Includes English, French, German and Turkish UI strings; other languages fall back to English.
- Main city only. This first version does not include cultural/era outposts, QI or other players' cities.

PRODUCTION VALUE NOTE
Production columns are derived from FoE Helper's building production model. They are useful for comparison/sorting,
but they are not guaranteed collection outcomes and do not imply a common hourly/daily interval across every building.
Random, conditional, limited-time and state-dependent outputs may not be completely represented.

SAFETY / TROUBLESHOOTING
- Keep the original extension disabled only while testing the development copy; re-enable it after turning the development copy off.
- If the development copy errors, open opera://extensions and use its Errors button (if shown), or inspect the Forge of Empires tab's DevTools Console.
- If this development copy does not load, remove ONLY the development copy from opera://extensions and turn the original store extension back on.
- Keep this extracted folder in place: unpacked extensions are loaded directly from it.
- This is an experimental add-on, not an official FoE Helper release.
