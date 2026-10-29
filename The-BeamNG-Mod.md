# The BeamNG mod

`beamntrack_mod.zip` is the small piece of BeamNTrack that runs **inside BeamNG.drive**. It has one job: read a few values about your car and the level ten times a second and send them to the BeamNTrack app on your own computer. It doesn't change the game, add vehicles or maps, or draw anything on screen.

The app does everything else: the map, the stats and the trip history. Without the mod the app stays on **Waiting for BeamNG...**

## Installing

1. Press `Win + R`, paste `%localappdata%\BeamNG\BeamNG.drive` and press Enter. If that folder doesn't exist, look in `Documents\BeamNG.drive`.
2. Open the folder for your game version (for example `current`), then the `mods` folder. Create `mods` if it is missing.
3. Copy `beamntrack_mod.zip` into `mods`. **Do not unzip it.**
4. Start (or restart) BeamNG.

No further setup is needed. The mod loads when the game starts. If it doesn't, open BeamNG's mod manager and check that it is enabled.

### Updating

Replace the old `beamntrack_mod.zip` in `mods` with the new one and restart BeamNG. Keep only **one** copy. Delete older copies, including the previous name `beampath_mod.zip`, so two versions don't compete.

Some app features need a newer mod than the first release:

| Feature | Needs |
| --- | --- |
| Route, speed, distance, car and level name | any version |
| Weather bar (time, dry or raining, wind) | the version that sends `tod`, `rain` and `wind` |
| Photo of your exact car configuration | the version that sends `config` |

If a feature is missing, the usual cause is an old mod zip.

### Uninstalling

Delete `beamntrack_mod.zip` from the `mods` folder and restart BeamNG. The mod leaves nothing else behind.

## What it sends

The mod sends one JSON message per update, as a UDP packet, to `127.0.0.1` port `4444`. That address is your own computer, so the data never leaves it.

```json
{
  "x": 512.3, "y": -204.7, "z": 31.2,
  "speed": 18.4,
  "vehicle": "etk800",
  "config": "854_type_a",
  "level": "west_coast_usa",
  "tod": 0.12,
  "rain": 0,
  "wind": 1.6,
  "wdx": 0.8, "wdy": 0.6
}
```

| Field | Meaning | Notes |
| --- | --- | --- |
| `x`, `y`, `z` | Position of your car in world metres | `y` points north. |
| `speed` | Speed in metres per second | The app converts it to km/h or mph. |
| `vehicle` | The car model's folder name | For example `etk800`. |
| `config` | The part configuration you are driving | The file name without `.pc`. Left out for custom or modified builds. |
| `level` | The level's identifier | For example `west_coast_usa`. `unknown` if the game gives none. |
| `tod` | Time of day | 0 is noon and 0.5 is midnight. |
| `rain` | Precipitation strength | 0 means dry. |
| `wind` | Wind speed | Metres per second. |
| `wdx`, `wdy` | Wind direction | A vector. |

A field is simply left out when the game doesn't provide it, and the app shows what it did receive.

### How often

- **Position, speed, car and level:** 10 times a second.
- **Weather and car configuration:** once a second. They change slowly, and looking them up costs more.

Nothing is sent when you aren't in a vehicle.

## How it works

The mod has two files:

- `scripts/beamntrack/modScript.lua` loads the extension when the game starts and keeps it loaded.
- `lua/ge/extensions/beamntrack.lua` is the extension. Each update it reads the player's vehicle, builds the message above and sends it through a non-blocking UDP socket. It runs in the game-engine Lua context.

Every lookup that might not exist in a particular game version is wrapped in a protected call. If one of them fails, that one value is left out and the rest still work. Errors go to the game log and never interrupt the game.

## Troubleshooting

**The app stays on "Waiting for BeamNG..."**

- Check that `beamntrack_mod.zip` is in the right `mods` folder, for the game version you actually run, and that it is **not unzipped**.
- Restart BeamNG after adding the mod.
- Make sure a vehicle is spawned. Nothing is sent when the game reports no player vehicle.
- Open the in-game console with `~` and look for lines containing `beamntrack`. On startup the mod logs the weather values it found and the car configuration, for example `environment values found: {...} config: 854_type_a`. A red line tells you which lookup failed.
- Make sure only one copy of the mod is installed.
- Check that no other program is using UDP port `4444` on your computer, and that a firewall isn't blocking local traffic.

**The weather bar is empty or partly empty**

Install the latest `beamntrack_mod.zip`. Some levels have no clouds or precipitation objects, so a value can be genuinely missing there.

**There is no car photo, or the config line is missing**

The mod only sends a configuration name for cars spawned from a saved part-config file. Custom or modified builds don't have one. If a normal config shows nothing, check the `config:` part of the console line above.

**The game updated and the mod stopped working**

BeamNG changes its internal Lua functions between versions. If the console shows errors from `beamntrack` after an update, open an issue and include the game version and the log lines.

## For developers

- The extension is the only code that runs in the game. All logic lives in the app.
- To test without the app, listen on UDP `127.0.0.1:4444` and print what arrives. For example with Python:

  ```python
  import socket
  s = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
  s.bind(("127.0.0.1", 4444))
  while True:
      print(s.recv(65535).decode())
  ```

  Close BeamNTrack first, because only one program can listen on the port.
- The source is in the `mod/` folder of the repository. To rebuild the zip, put the contents of `mod/` (the `lua` and `scripts` folders) at the top level of a new zip file. Don't zip the `mod` folder itself.
- To change the port, edit it in `beamntrack.lua` and in the app's `PORT` constant.

## Privacy

The mod sends only the values above, only to `127.0.0.1`. It has no account, no telemetry and no network access beyond the local UDP socket.

---

*BeamNTrack is an unofficial community project and is not affiliated with or endorsed by BeamNG GmbH.*
