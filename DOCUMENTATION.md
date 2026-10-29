Duplex client HTTP API

Base URL: http://127.0.0.1:65534

API Version: 2.0.3

Currently supported game versions: 1.26.3x-1.26.6x

Overview

The Duplex Client exposes a local HTTP API that allows external applications and custom UIs to communicate with the mod backend.

All endpoints are available through:

http://127.0.0.1:65534

IJN: Injection needed, this is a tag in the documentation to indicate that the backend must be injected before this endpoint can be used.

---

API Endpoints

"GET /"

Returns the current API/backend status.

Response

```json
{
  "injected": true,
  "injection_time": 3.304103374481201,
  "api_version": "2.0.3",
  "version": "1.26.52",
  "supported": "1.26.51",
  "sp_message": "ver_family"
}
```

if "injected" is false you have to use the API method /inject first before the client works.

The "injection_time" represents how long the client took to inject in seconds

The "api_version" may change when the backend updates,so don't rely on this documentation as this only updates when major changes to the user facing side are made.

The "version" /is the game version,the software is currently injected in. this value might alsow change due to injecting into different game versions, furthermore the currently supported versions might change without notice,the app will inject even though the version might not be supported indicated by the "sp_message" key in the json.

The "supported" key gives you information about what game version the injector used as base.

The "sp_message" key gives information about the compatibility between the client and the game, it can be "ver_family" if for example 1.26.52 isn't supported but 1.26.51 is so it's just falls back to the latest 5x version, it can be "fully" if the version is specifically supported and "latest" if the version has no supported version family and no supported version, it just falls back to the latest supported.

---

"GET /inject"

Injects the whole client into the game.

It is highly recommended to give this endpoint a very long timeout of around 90 seconds, as the backend might take a while to inject, especially on slower machines.

Response

```json
{
  "success": true
}
```

---

"GET /eject"

Ejects the current backend.

Response

```json
{
  "success": true
}
```

IJN

---

"GET /reload"

Reloads the backend, it's equivalent to /eject + /inject.

This method shouldn't be called usually

Response

```json
{
  "success": true
}
```

IJN

---

"GET /toggle/{module_id}/{toggle}"

Enables or disables a module.

Example

GET /toggle/reach/true

This enables the "reach" module.

To disable it:

GET /toggle/reach/false

Response

```json
{
  "success": true
}
```

IJN

---

"GET /set/{variable_name}/{value}"

Sets a variable to a new value.

Example

GET /set/reach/6.0

Response

```json
{
  "success": true
}
```

Out of range values will be rejected and will return a "success": false response, the same goes for invalid variable names and invalid values for the variable type.

IJN

---

"GET /get_config"

Returns the current configuration of all modules.

Response

```json
{
  "reach": {
    "toggle": true,
    "value": 6.0
  },
  "forcecords": {
    "toggle": false
  },
  "forcedisablecords": {
    "toggle": false
  },
  "hitbox": {
    "toggle": false,
    "value": 0.6
  },
  "phasefly": {
    "toggle": false
  },
  "speed": {
    "toggle": false,
    "value": 0.1000000015
  }
}
```

Example Usage

A frontend can call "/get_config" when opening or refreshing its UI to synchronize its controls with the current backend state.

IJN

---

"GET /get_modules"

Returns all modules exposed by the backend and their configuration metadata.

Response

```json
{
  "reach": {
      "injected": true,
      "category": "combat",
      "name": "Reach",
      "description": "Increases the distance you can hit entities.",
      "reinject": false
  },
  "reach_spoofer_core": {
      "injected": true,
      "category": "combat",
      "name": "Reach Spoofer Core",
      "description": "The configuration module to fix cross client reach spoofing.",
      "reinject": false
  },
  "reach_spoofer_oderso": {
      "injected": false,
      "category": "combat",
      "name": "Reach Spoofer Oderso",
      "description": "The module that handles reach spoofing for the OderSo client logic.",
      "reinject": true
  },
  "forcecords": {
      "injected": true,
      "category": "visual",
      "name": "Force Coordinates",
      "description": "Forces the game to always show coordinates.",
      "reinject": false
  },
  "forcedisablecords": {
      "injected": true,
      "category": "visual",
      "name": "Force Disable Coordinates",
      "description": "Forces the game to never show coordinates.",
      "reinject": false
  },
  "hitbox": {
      "injected": true,
      "category": "combat",
      "name": "Hitbox",
      "description": "Changes the size of entity hitboxes.",
      "reinject": false
  },
  "timer": {
      "injected": true,
      "category": "misc",
      "name": "Timer",
      "description": "Changes the game speed.",
      "reinject": false
  },
  "phasefly": {
      "injected": true,
      "category": "movement",
      "name": "Phase Fly",
      "description": "Allows you to fly through blocks.",
      "reinject": false
  },
  "speed": {
      "injected": false,
      "category": "movement",
      "name": "Speed",
      "description": "Increases your movement speed.",
      "reinject": false
  }
}
```

The "injected" key indicates whether the module is currently injected or not, this is useful for UIs to know which modules are available to be toggled. keep the "reinject" key in mind.

The "reinject" key indicates whether you can ignore the "injected" key and it will automatically try to inject the module when toggling it on, although this can fail to, in such a case "success": false will be returned.

Example Usage

A UI can use this endpoint to build its controls dynamically.

A frontend could therefore create:

Reach
[ ON / OFF ]

IJN

---

"GET /get_vars"

Returns all variables exposed by the backend and their metadata.

```json
{
  "reach": {
      "owner": "reach",
      "type": "<f",
      "min_value": 3.0,
      "max_value": 6.0,
      "default_value": 3.0,
      "name": "Reach",
      "description": "The distance you can hit entities."
  },
  "reach_spoof_min": {
      "owner": "reach_spoofer_core",
      "type": "<f",
      "min_value": 0.0,
      "max_value": 6.0,
      "default_value": 2.7,
      "name": "Minimum Reach Spoof",
      "description": "The minimum reach value to spoof."
  },
  "reach_spoof_range": {
      "owner": "reach_spoofer_core",
      "type": "<f",
      "min_value": 0.0,
      "max_value": 3.0,
      "default_value": 0.3,
      "name": "Reach Spoof Range",
      "description": "The range of reach values to spoof."
  },
  "reach_spoof_activation_threshold": {
      "owner": "reach_spoofer_core",
      "type": "<f",
      "min_value": 0.0,
      "max_value": 10.0,
      "default_value": 3.0,
      "name": "Reach Spoof Activation Threshold",
      "description": "The threshold at which reach spoofing activates."
  },
  "hitbox_width": {
      "owner": "hitbox",
      "type": "<f",
      "min_value": 0.6,
      "max_value": 10.0,
      "default_value": 0.6,
      "name": "Hitbox Width",
      "description": "The width of entity hitboxes."
  },
  "hitbox_height": {
      "owner": "hitbox",
      "type": "<f",
      "min_value": 0.6,
      "max_value": 10.0,
      "default_value": 1.8,
      "name": "Hitbox Height",
      "description": "The height of entity hitboxes."
  },
  "tps": {
      "owner": "timer",
      "type": "<f",
      "min_value": 0.1,
      "max_value": 40.0,
      "default_value": 20.0,
      "name": "TPS",
      "description": "The number of ticks per second the game runs at."
  }
}
```

The "owner" is the module that owns the variable, this is useful for UIs to group variables by their module.

The "type" is the data type of the variable, this is useful for UIs to know how to handle the variable, for example a float variable can be represented by a slider, while a boolean variable can be represented by a checkbox.

Example Usage

This endpoint is used to add value sliders to a frontend UI, for example a slider for the "reach" variable could be created with the following properties:

slider min: 3.0, max: 6.0, default: 3.0

Value
[---------●--]
3.0        6.0

IJN

---

Example Frontend Flow

A frontend can be initialised in the following way:

1. Check whether the backend is available

GET http://127.0.0.1:65534/

2. If the backend is not injected, inject it

GET http://127.0.0.1:65534/inject

3. Retrieve the modules, theire configuration metadata, and the injection status

GET http://127.0.0.1:65534/get_modules

4. Retrieve the variables and their metadata

GET http://127.0.0.1:65534/get_vars

5. Retrieve the current configuration of all modules

GET http://127.0.0.1:65534/get_config

6. Update the UI controls to reflect the current configuration

7. (Optional) Update the configuration of a module

GET http://127.0.0.1:65534/toggle/reach/true

GET http://127.0.0.1:65534/set/reach/6.0

---

Important Notes

"success": true indicates that the operation was successful.

The backend has no update feature, so you need to handle the following: (optional but recommended)

- ckeck if https://api.github.com/repos/duplex-client/duplex-client.github.io/releases/latest is newer than the current version, the tag will be the version number, for example "v2.0.3" is version 2.0.3, you can compare this with the "api_version" key in the response of the "/" endpoint.
- The names for the releases are "DuplexServer-standalone.zip" and "DuplexServer.exe" for the standalone and onefile builds respectively, you can use the "assets" key in the response of the releases/latest endpoint to find the download url for these files.
- Then download the latest release from the url key. you can decide if you want to use the standalone or onefile build but keep in mind that the onefile build is hated by the antivirus software and might be flagged as a false positive, the standalone build is less likely to be flagged but is larger in size and you have to extract it.

---

Quick Reference

Method| Endpoint| Purpose
|---|---|---|
"GET"| "/"| Check API/backend status
"GET"| "/inject"| Inject backend
"GET"| "/eject"| Eject backend
"GET"| "/reload"| Ejects and reinjects backend
"GET"| "/toggle/{module_id}/{toggle}"| Enable/disable a module
"GET"| "/set/{variable_name}/{value}"| Set a module value
"GET"| "/get_config"| Get current configuration of all modules
"GET"| "/get_modules"| Get all modules, their configuration metadata and theyre injection status
"GET"| "/get_vars"| Get all variables and their metadata.

sp_message Reference

Value| Meaning
|---|---|
fully| The game version is fully supported by the backend.
ver_family| The game version is not supported, but a supported version family exists. The backend will fall back to the latest supported version in that family.
latest| The game version is not supported, and no supported version family exists. The backend will fall back to the latest supported version, this can be either caused by a new game version being released, or the backend failing to read the game version correctly.

---

Base URL

http://127.0.0.1:65534

API Version: 2.0.3
Currently supported game versions: 1.26.3x-1.26.6x