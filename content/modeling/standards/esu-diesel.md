---
title: "LokSound Diesel Programming Standard"
linkTitle: 'LokSound Diesels'
tags: [programming, diesel]
manufacturers: ['ESU-Loksound', 'IAScaled']
---

## Overview
Documented below is a recommended standard for the configuration of an ESU/Loksound v5 decoder for a diesel locomotive, including the function mapping and relevant configuration steps.

This is designed to support seamlessly interchangeable operation with a standard throttle or a [ProtoThrottle](/modeling/manufacturers/iascaled/protothrottle)

Please note this standard is still undergoing testing. Please expect updates, and check the release notes for specific changes.

## Functions

Note: the below is an operators reference. See the configuration sections for the actual function mapping.

| # | Function | Type | Active in Consist | Notes |
| - | -------- | ---- | ----------------- | ----- |
| 0 | Headlight | Lighting | Yes |
| 1 | Bell | Sound |
| 2 | Horn | Sound |
| 3 | Coupler [^Coupler] | Sound |
| 4 | Brakes | Motor Control | Yes |
| 5 | Dynamic Brakes | Motor Control | Yes |
| 6 | Heavy Load | Motor Control | Yes |
| 7 | Dimmer | Lighting |
| 8 | Prime Mover | Motor Control | Yes |
| 9 | Ditch Lights | Lighting |
| 10 | Number Boards | Lighting |
| 11 | Marker Lights | Lighting |
| 12 | Switching Lighting | Lighting | Yes | Both headlights dim |
| 13 | Extra Lighting | Lighting |
| 14 | Extra Lighting | Lighting |
| 15 | Extra Lighting | Lighting |
| 16 | Extra Lighting | Lighting |
| 17 | Random 1 (Radiator) | Random |
| 18 | Random 2 (Sanders) | Random |
| 19 | Random 3 ( Short Air Let Off) | Random | Yes[^PTAIR] | ProtoThrottle air test |
| 20 | Random 4 (Compressor) | Random | Yes[^PTAIR] | ProtoThrottle air test |
| 21 | Random 5 (Air Dryer) | Random |
| 22 | Front Dim/Ditchlights [^PTLight] | ProtoThrottle |
| 23 | Front Bright/Ditchlights [^PTLight] | ProtoThrottle |
| 24 | Reverser Center | ProtoThrottle | Yes |
| 25 | Random 6 (Shutters) | Random |
| 26 | Rear Dim/Ditchlights [^PTLight] | ProtoThrottle |
| 27 | Rear Bright/Ditchlights [^PTLight] | ProtoThrottle |
| 28 | Not Currently Used |
| 29 | Not Currently Used |
| 30 | Coast | Motor Control | | For dynamic sound control |
| 31 | Notch 8 | Motor Control | | For dynamic sound control |

[^Coupler]: Custom coupler logic should be used, information coming soon

[^PTLight]: See ProtoThrottle notes on condensed lighting control

[^PTAIR]: Used by ProtoThrottle Air Test feature

## Configuration

Below are all the steps required to change a default LokSound file to match the diesel standard. Generally speaking, only the setting that need to be changed will be mentioned.

### Address

#### Active functions in consist mode

The following should be active:
* F4 Brake
* F5 Dynamic Brake
* F6 Heavy Load
* F8 Prime Mover
* F12 Switching Lighting
* F19 Short Air Let Off[^PTAIR]
* F20 Compressor[^PTAIR]
* F24 Reverser

### Analog Settings

#### Enable DC Analog Mode

```
Enable DC analog mode: disabled
```

Unless it is essential that the locomotive be usable on a DC layout, disable DC Analog Mode. Leaving this enabled can cause runaway locomotives during certain layout issues.

### Brake Settings

#### Brake functions

Currently all brake functions are left at their default values:

| Brake | Reduction | Max Speed |
| ----- | --------- | --------- |
| Brake 1 | 128 / 50.2% | 126 |
| Brake 2 | 128 / 50.2% | 126 |
| Brake 3 | 240 / 94.12% | 126 |

If `Max Speed` is set to 0 and the brake is left applied, a stopped locmotive will be unable to move. Leaving `Max Speed` set to 126 will allow the locomotive to move from a stop. With the deceleration rate set to allow coasting, requiring a brake to stop, normal stopping behavior can be achieved by leaving Max Speed at 126 and running with the brake on.

### DCC Settings

#### RailCom settings

```
Leave all RailCom fields enabled
```

Historically the recommendation has been to disable RailCom. However with the seeming arrival of hardware that sanely supports RailCom (TCS, etc), the recommendation is now to leave this enabled.

#### Speed step mode

This should be configured correctly by default on v5 DCC decoders, but ensure the following:

```
Deselect 'Detect speed step mode automatically'
Select 'Use 28 or 128 speed steps'
```

14 speed step mode is a legacy mode, which should never be encountered. Disabling it removes the chance of unexpected behavior.

### Driving Characteristics

#### Acceleration and deceleration

```
Time from maximum speed to stop: 255
```

This standard is designed to configure locomotives to coast, requiring a brake to stop. This is needed for proper operation with a ProtoThrottle, and is preferred for more realistic operation with a regular throttle. The maximum deceleration time should be acceptable for most scenarios. If a lower value is desired, it must be set consistently across all units in a consist, and should be kept consistent across an entire fleet.

#### Load Adjustment

TBD: Changes may be needed here, further research is required.

#### Starting delay

```
Delay starting if drive sound is enabled: disabled
```

Leaving this enabled will prevent your locomotive from begininning to move while startup sounds are playing. With the addition of the 'Persist function' setting, this is likely less necessary, but for now the standard is still to disable it.

### Function Outputs

#### Configuring headlights for ProtoThrottle operation

Configure Front headlight dim:
```
 Output: Front light [2]
 Name:   Front Headlight (Dim)
 Output Mode: Dimmable headlight (fade in/out)
 Brightness: 5
 Special Functions: LED Mode
```

Configure Rear Headlight Dim:
```
 Output: Rear light [2]
 Name:   Rear Headlight (Dim)
 Output Mode: Dimmable headlight (fade in/out)
 Brightness: 5
 Special Functions: LED Mode
```

#### Configuring other lighting functions

See the following for instructions on configuring other lighting functions:

[Configuring Lighting for Loksound Decoder]

[Configuring Class Lights Using RGB LEDs]

### Function Settings

#### Random functions

Random 1:
```
 Triggered function: F17
```

Under this standard, random function 1 is moved from F11 to F17. This groups it with the other random functions, and prevents it from taking up a function slot that is much more easily accessible. Make sure that this is changed both here, and under function mapping.

### Function Mapping

Marks in the first column indicate the following:
- R: Required configuration to meet the standard
- O: Optional configuraton, model dependent
- P: ProtoThrottle only [^PTLight]
- D: Dynamic Sound Control only

All other functions slots should match the default mapping, but it is always worth verifying.

|   | Name              | Conditions | Physical Outputs | Logical Functions | Sounds |
| - | ----------------- | ---------- | ---------------- | ----------------- | ------ |
|   | Front Headlight   | Fwd, F0    | Front Light [1]  |
|   | Rear Headlight    | Rev, F0    | Rear Light [1]   |
|   | Bell              | F1         |                  |                   | [4] Bell |
|   | Horn              | F2         |                  | Grade Crossing    | [3] Horn |
|   | Coupler           | F3         |                  |                   | Coupler [^Coupler] |
| R | Brake             | F4         |                  | Brake 3           |          |
| R | Brake [1]         | not F4     |                  | Disable Brake Sound  |
| R | Dynamic Brake     | F5         |                  | Shift 1 (Dynamics)   | [6] Dynamic Brakes |
| R | Heavy Load        | F6         |                  | Shift 6 (Heavy Load) |
| R | Dimmer            | F7         |                  | Dimmer            | [24] Short Air Let Off |
|   | Engine Sounds     | F8         |                  |                   | [1] Prime Mover Sound |
| R | Engine Sounds [2] | F8         |                  |                   | [29] Brake Automatic Set/Release |
|   | Engine Sounds     | Stop, F8   |                  |                   | [19] Reverser |
| R | Engine Sounds [3] | Stop, F8, not F15 |  Update This | Update This | [26] Starting Delay |
|   | Engine Sounds     | Drive, F8  |                  |                   | [25] Traction Motors |
| R | Engine Sounds [4] | not F8     |                  |                   | [18] Air Dryers on Shutdown |
| O | Ditchlights*      | Fwd, not F7, F9, not F12 | Front Ditchlights |
| O | Ditchlights*      | Rev, not F7, F9, not F12 | Rear Ditchlights |
| O | Number Boards*    | F10        | Number Boards    |
| O | Marker Lights*    | F11        | Marker Lights    |
| R | Switching Mode*   | F12        | Front & Rear Headlight | Dimmer |
| O | Extra Lighting*   | F13        | Extra Lighting   |
| O | Extra Lighting*   | F14        | Extra Lighting   |
| O | Extra Lighting*   | F15        | Extra Lighting   |
| O | Extra Lighting*   | F16        | Extra Lighting   |
| R | Random Sound 1    | F17        |                  |                   | [8] Radiator |
|   | Random Sound 2    | F18        |                  |                   | [13] Sanding Valve |
|   | Random Sound 3    | F19        |                  |                   | [24] Short Air Let Off |
|   | Random Sound 4    | F20        |                  |                   | [7] Air Compressor |
|   | Random Sound 5    | F21        |                  |                   | [17]  Air Dryer |
| P | Front Dim         | F22, not F23 | Front Light [2] (Dim) |
| P | Front Bright      | not F22, F23 | Front Light [1] |
| P | Front Bright & Ditch | F22, F23  | Front Headlight [1], Front Ditchlights |
|   | Reverser Center   | F24        |                  | Shift 5 (Reverser) | [20] Reverser Center |
|   | Random Sound 6    | F25        |                  |                   | [32] Shutters |
| P | Rear Dim          | F26, not F27 | Rear Light [2] (Dim) |
| P | Rear Bright       | not F26, F27 | Rear Light [1] |
| P | Rear Bright & Ditch | F26, F27 | Rear Light [1], Rear Ditchlights |
| D | Coast             | F30        |                  | Shift 3 (Coast) |
| D | Run 8             | F31        |                  | Shift 2 (Run 8) |

### Identification

This is currently unused, but that should probably change.

### Compatibility

#### Broadway Limited Steam Engine Control

[Configure LokSound for BLI Steam]

### Motor Settings

[LokSound Speed Matching]

### Smoke Unit

Section Coming Soon

### Special Options

#### Memory Settings

```
Persist functions: enabled
```

Enabling this setting prevents having to re-enable functions after a locomotive temporarily loses power, due to a short, etc. 

### Sound Settings

#### Steam Chuffs

[Configure LokSound for BLI Steam]

#### Brake Sounds

Todo: test these settings

#### Dynamic Sound Control

Section Coming Soon

### Sound Slot Settings

Section Coming Soon

## Release Notes

* 2026-09-13
  * Formatting cleanup
  * Adding and clarifying notes
* 2026-05-08
  * Completed outline
* 2026-05-04
  * Initial document creation
