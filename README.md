# Reolink: temporary fix for local devices reported as "account device"

This is a **temporary** copy of Home Assistant's built-in Reolink integration (from Home Assistant 2026.10.1). It changes one thing: it uses a patched `reolink-aio` library that fixes [starkillerOG/reolink_aio#225](https://github.com/starkillerOG/reolink_aio/issues/225).

## The problem it fixes

`reolink-aio` 0.21.18, which ships with Home Assistant 2026.10.1, stops with this error as soon as the device advertises the newer `sigVer v3` login:

```
The Reolink device is set up as an 'account device', which blocks local login.
```

Some devices advertise `sigVer v3` even though the Reolink app shows them as a **Local Device**, for example a Home Hub on firmware `v3.3.0.601_26082942`. Those devices still accept the normal local login. The patched library ([starkillerOG/reolink_aio#226](https://github.com/starkillerOG/reolink_aio/pull/226)) tries that login first. It only reports "account device" if the device actually rejects the login.

Discussion: [home-assistant/core#184726](https://github.com/home-assistant/core/issues/184726)

## What installing it does

- Home Assistant loads a custom integration with the same name (`reolink`) in place of its built-in one. Your existing Reolink devices, entities, automations, and settings are kept, because they belong to the `reolink` integration either way.
- On startup, Home Assistant installs the patched `reolink-aio` from GitHub instead of 0.21.18.
- Home Assistant shows the warning "We found a custom integration reolink which has not been tested by Home Assistant". This is expected.
- The copy is frozen at the 2026.10.1 version of the integration. Remove it once a Home Assistant release includes the fix.

## Requirements

- Home Assistant **2026.10.1**. If you rolled back to 2026.10.0, update to 2026.10.1 first.
- [HACS](https://hacs.xyz/) installed.

## Install

1. In Home Assistant, open **HACS**.
2. Open the **⋮** menu (top right) → **Custom repositories**.
3. **Repository:** `https://github.com/andrebrait/ha-reolink-sigver-fix`. **Type:** `Integration`. Click **Add**, then close the dialog.
4. Search HACS for **Reolink (sigVer v3 local-device fix)**, open it, and click **Download**.
5. Restart Home Assistant: **Settings** → **System** → **⏻** (top right) → **Restart Home Assistant**.

Your Reolink devices should come back after the restart. Downloading the patched library can make the first startup take a minute longer.

## Revert to the version shipped by Home Assistant

1. Open **HACS**, find **Reolink (sigVer v3 local-device fix)**, open the **⋮** menu → **Remove**, and confirm.
2. Restart Home Assistant.

Home Assistant then uses its built-in Reolink integration again. Your devices, entities, and automations are kept. Optionally, remove the custom repository from **HACS** → **⋮** → **Custom repositories**.

Do this once a Home Assistant release includes the fix. Until then, reverting brings the "account device" error back.
