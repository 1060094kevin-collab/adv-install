# adv-install

`adv-install` is a non-root Termux + Shizuku shell workflow for installing locally stored APK files through Android Package Manager as `uid=2000(shell)`.

The project is intended as an on-device shell installation route in response to Android Developer Verification: it uses Shizuku and `rish` to run Package Manager through the Android shell identity normally associated with ADB, without root and without requiring a USB-connected computer for every APK installation after setup.

> [!IMPORTANT]
> Google documents a Developer Verification exception for Android Debug Bridge (ADB) installs. Google does not explicitly name Shizuku, `rish`, Termux, or `adv-install`. This project is designed to use the same Android shell identity as ADB, but future Android, Google Play services, or manufacturer changes could classify this workflow differently.

## Quick start

1. Install Termux and Shizuku on your Android device.
2. Start Shizuku using wireless debugging.
3. Export `rish` and `rish_shizuku.dex` from Shizuku.
4. Follow the full setup guide in docs/installation.md.
5. Install a local APK:

```bash
adv-install "/storage/emulated/0/Download/application.apk"
```

## Workflow

```text
Local APK or APK archive
        |
        v
Termux
        |
        v
adv-install
        |
        v
rish + Shizuku
        |
        v
uid=2000(shell)
        |
        v
/data/local/tmp staging
        |
        v
Android Package Manager
        |
        v
Installed app
```

## What adv-install does

`adv-install`:

- accepts a local `.apk`, `.apks`, `.xapk`, `.zip`, or directory of split APKs;
- verifies that `rish` returns Android shell UID `2000`;
- stages APK files under `/data/local/tmp`;
- validates staged byte counts;
- calls `pm install` or `pm install-multiple` through `rish`;
- removes local and remote temporary files after success, failure, or interruption.

## Usage examples

Install a single APK:

```bash
adv-install "/storage/emulated/0/Download/application.apk"
```

Install an `.apks` archive:

```bash
adv-install "/storage/emulated/0/Download/application.apks"
```

Install an `.xapk` archive:

```bash
adv-install "/storage/emulated/0/Download/application.xapk"
```

Install a `.zip` archive containing APK files:

```bash
adv-install "/storage/emulated/0/Download/application.zip"
```

Install a directory containing split APK files:

```bash
adv-install "/storage/emulated/0/Download/application-splits"
```

Fresh reinstall for the current Android user:

```bash
adv-install --fresh --package com.example.app "/storage/emulated/0/Download/application.apk"
```

See docs/usage.md for all options and archive behavior.

## Documentation

- docs/scope.md - project goals, Developer Verification scope, and what the tool does not bypass.
- docs/installation.md - Termux, Shizuku, `rish`, and first-time setup.
- docs/usage.md - supported input types, options, examples, and archive behavior.
- docs/technical-overview.md - staging, byte-count validation, shell identity checks, and Package Manager flow.
- docs/security-considerations.md - APK trust, hash checks, Shizuku handling, and data behavior.
- docs/troubleshooting.md - common setup and install failures.
- docs/faq.md - common questions about Developer Verification, ADB-style installs, root, Play Protect, and split APKs.
- docs/maintenance.md - updating, removing, testing, and release checklist.

## Non-goals

`adv-install` is not an APK downloader, APK store, malware scanner, root tool, Play Protect disabler, signature bypass, enterprise-policy bypass, or guarantee of future Android compatibility.

The APK must still pass Android's normal Package Manager checks, including signature validation, package parsing, compatibility requirements, user/device policy, and manufacturer restrictions. See docs/scope.md for the detailed boundary list.

## Install from this repository

After cloning:

```bash
install -m 755 adv-install "$PREFIX/bin/adv-install"
hash -r
```

Then verify:

```bash
command -v adv-install
bash -n "$PREFIX/bin/adv-install"
```

## Test

```bash
bash -n adv-install
bash tests/run-tests.sh
```

If ShellCheck is installed:

```bash
shellcheck adv-install
```

The test harness uses mocked `rish` and mocked `pm` commands. It validates script behavior and cleanup logic without requiring Android, Shizuku, root, or a writable real `/data/local/tmp`.

## Responsible use

Use this project only on devices controlled by you or devices where you are authorized to perform installation and testing.

Do not use this project to install malicious software, interfere with another person's device, conceal unauthorized installation activity, or evade organizational policy on managed devices.

## Official references

- [Android Developer Verification](https://developer.android.com/developer-verification)
- [Android release notes](https://developer.android.com/about/versions/16/qpr2/release-notes)
- [Shizuku](https://shizuku.rikka.app/)
- [Shizuku setup guide](https://shizuku.rikka.app/guide/setup/)
- [Shizuku downloads](https://shizuku.rikka.app/download/)
- [`rish` documentation](https://github.com/RikkaApps/Shizuku-API/blob/master/rish/README.md)
- [Termux app](https://github.com/termux/termux-app)

## License

Distributed under the MIT License. See LICENSE.

## Disclaimer

This project is not affiliated with, endorsed by, or sponsored by Google, Android, Shizuku, RikkaApps, Termux, or their respective maintainers.

Android is a trademark of Google LLC.

The project is provided without a guarantee that future Android releases, Google Play services updates, manufacturer changes, or Developer Verification policy changes will preserve current behavior.
