# PancakeStore LE 1.1 — Server version lookup hardening

## Source baseline

`PancakeStore-LE-v1.0-IPA-export-direct-long-press-Nov25.zip`

## Changes

- Replaced unsafe Server version-history parsing (`try!`, forced casts and forced field unwraps) with checked JSON handling.
- Handles array responses, a single version object, keyed version objects, empty arrays, `data: null`, malformed records and duplicate external identifiers without crashing.
- Validates the App Store application ID, HTTP response status and response body before presenting version choices.
- Runs native ipatool version lookup off the main queue so the UI can display `Getting Versions…` while the list is loading.
- Uses a separate `isGettingVersions` state for both native and Server version retrieval. `Downgrading Application…` is shown only after a version is selected and its IPA download begins.
- Resets loading state on empty/error responses and when version-selection alerts are cancelled.
- Keeps the successful installation state active so the existing success panel, Open App action and direct long-press IPA export remain available.
- Updated `MARKETING_VERSION` from `1.0` to `1.1` in all four app/share-extension Debug/Release configurations.
- Added `status.gettingVersions` to all 12 existing localizations.

## Preserved

- Native Store authentication and its saved Anisette/keychain/cookie flow.
- Embedded ipatool bridge and version lookup API.
- Existing IPA download/install handoff.
- Existing Open App long-press direct Share Sheet export.
- No IPA Decrypt experiment or Decrypt UI was added.

## Validation

- All 8 Swift source files passed `swiftc -frontend -parse`.
- All 12 localization files and both Info.plists plus the Xcode project passed `plutil -lint`.
- The isolated Server parser passed tests for empty array, null data, single version, multiple versions with duplicates/malformed records, a single-object response, keyed-object response, unsupported data and invalid JSON.
- Confirmed the native `IPATool.swift`, `NativeIPAToolBridge.swift`, `bridge.h` and Go bridge source are byte-identical to the supplied baseline.
- Full Xcode/iOS build and on-device tests were not available in this environment.
