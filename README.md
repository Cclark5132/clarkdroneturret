# Clark Drone Turret

A webcam-guided airsoft turret that tracks and shoots small targets indoors. Phase 1 targets a colored ball on a string. Drones come later.

A laptop runs Python + OpenCV to find the target and sends aim/fire commands over USB to an Arduino UNO R4 WiFi, which drives two servos (pan/tilt) and a MOSFET that fires an electric airsoft pistol.

## Status

- [x] Parts ordered (Nov 5, 2026)
- [ ] Servo bench test
- [ ] Fire control bench test (LED in place of the gun)
- [ ] Mount CAD and printing
- [ ] Gun modification and integration
- [ ] Vision and aiming
- [ ] Drones

## Repo layout

| Path | Contents |
| --- | --- |
| `BOM.md` | Every part used, with quantities and prices |
| `PLAN.md` | Build plan, wiring, serial protocol, design calcs |
| `LOG.md` | Build log, newest first |
| `firmware/` | Arduino code |
| `vision/` | Python tracking and aiming code |
| `cad/` | CAD files and STLs |
| `photos/` | Build photos and test videos |

## Safety

Sealed goggles on, people and pets out of the room, and a BB trap behind the target whenever the gun is loaded. The firmware will not fire unless the arm switch is on, and it cuts the trigger if the laptop stops sending for 300 ms.
