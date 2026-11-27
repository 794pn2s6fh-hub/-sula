# IPA export from Open App context menu

Base: `PancakeStore-LE-v1.0-first-login-assets-fix-Nov24.zip`

## Changes

- Added an `Export IPA` context-menu item to the existing `Open App` button. No separate visible button was added.
- The context menu entry is present only while `store.hasServedApp` is true, matching the existing enabled state of `Open App`.
- The downloaded IPA URL is retained in `StoreData` after the native ipatool download has completed and the file has been verified.
- Selecting `Export IPA` copies the current package to a descriptively named temporary `.ipa` file and opens the same share-sheet helper already used by log export.
- Added the menu label to all 12 existing localizations.

## Decryption assessment

The iOS download path in `ThirdParty/ipatool/bridge/ios/download.go` requests `PlatformIPhone`, then `pkg/appstore/appstore_download.go` follows the mobile package path and `runDownload` calls `ReplicateSinf` to add the App Store FairPlay data. The existing `StoreAgent` decrypter in `pkg/appstore/appstore_download_macos.go` handles macOS package downloads (`.pkg`/XAR), not iOS IPA executables. The iOS-on-macOS test also asserts that the mobile package path does not initialize that macOS decrypter.

No iOS IPA executable decryption was added. Removing `.sinf` data would not decrypt the Mach-O executable and could invalidate the installable package. Export therefore preserves the App Store FairPlay-protected IPA returned by the current native pipeline.

## Validation

- Swift parser check passed for the modified `ContentView.swift` and `Downgrader.swift`.
- All 12 `.strings` files passed `plutil -lint`; each contains exactly one `action.exportIPA` entry and no duplicate keys.
- An Xcode/iOS build and on-device share-sheet test were not run in this environment.
