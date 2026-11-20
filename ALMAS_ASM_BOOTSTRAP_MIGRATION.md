# Almas Termux ASM package migration

Target Android application ID: `com.almas.asm`.

This branch changes the Android install identity and the Java/resource constants so the app can coexist with the upstream Termux app. The Java namespace remains `com.termux` because it is the source-code namespace, not the installed application ID.

## Important bootstrap blocker

The upstream bootstrap archives downloaded by `app/build.gradle` are built with the upstream Termux prefix:

`/data/data/com.termux/files/usr`

Almas ASM requires its own prefix:

`/data/data/com.almas.asm/files/usr`

Changing the application ID and Java constants does not rewrite the absolute prefix embedded in ELF binaries and scripts. Therefore, the stock upstream bootstrap must NOT be shipped in an Almas ASM APK. Doing so can produce an APK that installs but fails to initialize its terminal environment.

## Required before a usable APK

1. Build the `apt-android-7` bootstrap packages from the Termux packages build system with the Almas prefix `/data/data/com.almas.asm/files/usr`.
2. Publish the resulting architecture-specific bootstrap archives and checksums to a controlled release location.
3. Change the Gradle bootstrap download task to fetch only those Almas archives and verify their SHA-256 checksums.
4. Add CI checks that reject upstream bootstrap archives or binaries containing the old prefix.
5. Build and test the ARM64 APK on a clean Android installation, verify first-run bootstrap, shell startup, package installation, and persistence.
6. Only then enable the release workflow and signed update channel.

No usable APK should be published from this branch until those checks pass.
