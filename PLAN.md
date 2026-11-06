# Build Plan

## How it works

1. Webcam grabs a frame (1280x720, 60 fps).
2. Python/OpenCV finds the ball and its center pixel.
3. Python converts the pixel error to a pan/tilt correction and sends `A <pan> <tilt>`.
4. Arduino moves the servos.
5. When the ball stays centered for 5 frames, Python sends `F <ms>`. The Arduino fires only if armed.
6. If the laptop goes quiet for 300 ms, the Arduino turns the MOSFET off.

## Stages

| Stage | Build | Done when |
| --- | --- | --- |
| 2A Servo bench test | Servos on the breadboard, typed serial commands | `A 90 45` moves both servos there |
| 2B Fire control bench test | MOSFET + LED instead of the gun, arm switch, watchdog | All bench safety tests pass |
| 2C Mount CAD + print | Base, turntable, uprights, tilt cradle, camera clip, tray | Pans/tilts smoothly, gun balanced |
| 2D Gun mod + integration | MOSFET takes over the trigger circuit | `F` fires a predictable number of BBs |
| 3 Vision + aiming | Tracker, PID aim, lock-on, calibration | Hits a swinging ball reliably |
| 4 Drones | Detection model, prediction | Later |

## Pin map

| Pin | Connection |
| --- | --- |
| D9 | Pan servo signal |
| D10 | Tilt servo signal |
| D7 | MOSFET module input (10 k pull-down to GND) |
| D2 | Arm switch to GND (`INPUT_PULLUP`, LOW = armed) |
| GND | Common ground with the 6 V servo supply |

Power: 6 V supply for servos only, gun battery through the MOSFET and a kill switch, USB for the Arduino. All grounds tied together except the isolated gun side.

## Serial protocol (115200 baud, one command per line)

| Command | Meaning | Reply |
| --- | --- | --- |
| `A <pan> <tilt>` | Aim, degrees | none |
| `F <ms>` | Fire, capped at 300 ms | `FIRED` or `SAFE` |
| `P` | Ping, feeds the watchdog | `OK` |
| `H` | Home | none |

## Firing rules

1. MOSFET pin set LOW first thing in `setup()`.
2. Fire only if armed and a message arrived in the last 300 ms.
3. Shot length capped at 300 ms.
4. If the arm switch is already on at power-up, stay safe until it is cycled.
5. No `delay()` while firing; the shot ends on a `millis()` timer.

## Mechanical

**Pan base:** 4 x 608ZZ under the turntable rim, 90 deg apart, each on an M3 screw through a printed 8 mm sleeve, rolling on a printed track ring. The pan servo sits in the center and only turns/centers the turntable through its horn.

**Tilt:** cradle between two uprights. Tilt servo on one side, 608ZZ on the other. Tilt axis goes through the center of mass of gun + camera.

**Fits to test-print first:**

| Feature | Nominal | Test sizes |
| --- | --- | --- |
| 608ZZ pocket | 22.0 mm | 22.0 / 22.1 / 22.2 / 22.3 |
| 8 mm sleeve | 8.0 mm | 7.9 / 8.0 / 8.1, 3.3 mm M3 hole |
| M3 clearance | 3.0 mm | 3.3-3.4 |
| M3 nut trap | 5.5 mm flats | 5.7-5.8 |

## Design calcs

Servo pulse:

    pulse (us) = 500 + 11.1 x angle (deg)
    1 us = 0.09 deg

Servo torque at 6 V:

    22.8 kg-cm x (6 / 6.8) = about 20 kg-cm

Tilt load (gun ~0.5 kg + camera 0.076 kg + cradle ~0.15 kg = ~0.73 kg):

| CoM offset | Holding torque | Safety factor |
| --- | --- | --- |
| 1 cm | 0.73 kg-cm | 27 |
| 3 cm | 2.2 kg-cm | 9 |
| 5 cm | 3.7 kg-cm | 5.5 |

Tilt acceleration (gun as a 22 cm rod, 90 rad/s^2):

    I = (1/12) x 0.73 x 0.22^2 = 0.0029 kg-m^2
    torque = 0.0029 x 90 = 0.26 N-m = 2.7 kg-cm

Pan acceleration (1.2 kg on the turntable, 6 cm radius of gyration):

    I = 1.2 x 0.06^2 = 0.0043 kg-m^2
    torque = 0.0043 x 90 = 0.39 N-m = 4 kg-cm

Camera (73 deg diagonal FOV, 16:9, 1280 px wide):

    horizontal FOV = 65.6 deg, vertical FOV = 39.8 deg
    65.6 / 1280 = 0.051 deg per pixel
    focal length f = 640 / tan(32.8 deg) = 993 px
    distance d = f x ball diameter / ball width in px

Parallax (camera h = 5 cm above barrel):

| Distance | Error = atan(h / d) |
| --- | --- |
| 1 m | 2.9 deg |
| 2 m | 1.4 deg |
| 3 m | 1.0 deg |

Swinging ball (1 m string, 0.3 m amplitude):

    v max = 0.3 x sqrt(9.81 / 1) = 0.94 m/s
    in ~0.15 s of total latency it moves ~14 cm

So: fire at the ends of the swing first, then add lead.

All estimates get replaced with measured values as the build goes.
