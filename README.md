# miocar-apps

Signed app catalog for **CarStore** (`miocar.store`), the app store for the MioCar head unit (Android 9).

The app downloads `catalog.json` and `catalog.json.sig` from
`https://raw.githubusercontent.com/auroraphtgrp01/miocar-apps/main/` and only accepts the pair when the
signature verifies with the public key compiled into the app. APK files are attached to GitHub Releases
(tag `<package>-<versionCode>`), and every file is checked against its `sha256` and the entry's
`signerSha256` before it is installed.

## Files

- `catalog.json` - schema 1: `{"schema": 1, "seq": N, "generated": "...", "apps": [...]}`. Each app has
  `name`, `package`, `versionCode`, `versionName`, `files` (`name`, `url`, `sha256`, `size`; split sets list
  the base APK first), `signerSha256` (SHA-256 of the signing certificate), `grants` (from
  `background, location, microphone, overlay, pip, storage`), `icon`, optional `description` and `minSdk`.
  Canonical form: sorted keys, 2-space indent, UTF-8, trailing newline, apps sorted by package.
- `catalog.json.sig` - base64 DER ECDSA P-256 / SHA-256 signature over the exact bytes of `catalog.json`.
- `icons/<package>.png` - app icons.

`seq` goes up by one on every change; the app refuses a catalog with a lower `seq` than the one it has
already accepted.

## Editing

Never edit `catalog.json` by hand (the signature would break). Use the `carstore` CLI from the MioGeely
project:

```sh
cli/carstore.sh add app.apk --catalog-dir ~/Desktop/Codespace/miocar-apps --grants overlay,background
cli/carstore.sh remove some.package --catalog-dir ~/Desktop/Codespace/miocar-apps
cli/carstore.sh verify --catalog-dir ~/Desktop/Codespace/miocar-apps
```

The private signing key stays on the owner's machine (`~/.config/carstore/catalog-key.pem`) and is never
committed here.
