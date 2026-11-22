Build "PIXEL SKIES": a pixel-art flight game in a single HTML file. Canvas 2D only. No three.js, no 3D libraries, no meshes, no image files. Fake the 3D with sprite stacking.

Renderer
- Draw into a tiny framebuffer (about 300 px tall, width from the screen ratio) using a Uint32Array and putImageData. Scale it up to the full screen with image-rendering: pixelated.
- The plane is 16 slices of 32x32 palette indices (cream body, purple wings, yellow wing tips, mint cockpit, a dark outline slice at the bottom). Draw each slice 1 px higher than the one below, rotated by heading and roll.

World
- A 512x512 wrapping height map from tileable value noise (4-5 octaves). Heights are whole numbers so the land looks terraced.
- Height to color bands: deep sea, shallow sea, surf, sand, light grass, grass, forest, rock, snow. Light each pixel by comparing it with its neighbor so terraces get highlights.
- A second wrapping noise map for clouds at a fixed altitude, drifting slowly, with shadows on the ground.

Drawing
- Walk the ground rows from near to far. Each sample is a column: the top color at its height, a darker wall below it.
- Keep an occupancy mask: a pixel that has been written is never overwritten. No z-buffer.

Camera
- project(x, y, z): s = S / (1 + q*depth); screenX = cx + s*across; screenY = cy - s*(z + slope*depth).
- q = 0 gives a top-down view. Lerp q, slope, S and heading into a chase view behind the plane, with distance haze.
- The title screen is top-down on autopilot. Tapping to start swoops the camera into the chase view without a cut. A VIEW button (or V) switches between them.

Gameplay
- The plane always flies forward. Finger or mouse position steers: left/right turns, up/down climbs and dives (keys: arrows or WASD). Shift or holding the mouse boosts.
- Maneuvers: ROLL (tap, Space or the ROLL button) is a barrel roll that sidesteps and dodges anything it touches. LOOP (double tap, E or the LOOP button) is a full vertical loop with the camera following. Both have buttons on screen for phones.
- Glowing yellow suns float in chains at different heights. Each one gives fuel and +10 points. Chain them quickly for a combo multiplier up to x5; missing one breaks the combo.
- Storms: dark spiky orbs with a blinking red core. Hitting one costs fuel and the combo. Rolling or looping through one is a DODGE bonus. Passing close without touching is a CLOSE CALL bonus.
- Trick bonuses: a loop scores points, more if you grab a sun during it. Skimming low over the water scores slowly.
- Snowy peaks and hills: hitting terrain costs a lot of fuel, with a flash, a thud and screen shake.
- A new level every 25 seconds: faster, more storms, suns spread over more altitudes, storms guarding suns from level 3, moving storms from level 4. Fuel drains a bit faster each level. At zero fuel the plane spirals down.
- Floating text pops up for every bonus. An altitude bar sits next to the fuel bar. Every sun and storm casts a shadow on the ground so you can read its height.

Screens (HTML overlay, pixel font "Silkscreen")
- Title: "GOOMI LAB #01", "PIXEL SKIES", one line of how to play, "TAP TO FLY".
- In game: score, suns and level, combo, fuel bar, altitude bar, ROLL / LOOP / VIEW buttons.
- Game over: score, level reached, best combo, tricks, best score saved in localStorage (wrap it in try/catch), "TAP TO FLY AGAIN".
- Credit "made by @goomi_studio". Sound effects with Web Audio, starting only after the first tap. Must run smoothly on a phone.

Build it one part at a time: renderer, plane, terrain, clouds, camera, gameplay. After each part, run it, look at a screenshot, fix it, then continue.
