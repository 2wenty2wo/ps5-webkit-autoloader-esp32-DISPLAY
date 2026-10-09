# Maintaining this fork

This repo tracks `owendswang/ps5-webkit-autoloader-esp32` and adds the
0.96" LCD display support on top of it (SSID shown as "PS5 Master Key",
plus IP/WiFi/password on screen).

## Syncing upstream changes

```sh
git fetch upstream
git merge upstream/main
```

If upstream touched a file we've customized (currently: `esp32-arduino.ino`,
`Makefile`, `install-deps.sh`, `.gitignore`), git will flag a conflict.
Resolve it by keeping both sides' changes — never just take one side
wholesale, since upstream fixes and our LCD code both need to survive.

Once resolved:

```sh
git add <resolved files>
git commit -m "Merge upstream <version>, keep LCD display"
make s3-lcd   # confirm it still builds before pushing
git push
```

(First-time setup, if `upstream` isn't added yet:)
```sh
git remote add upstream https://github.com/owendswang/ps5-webkit-autoloader-esp32.git
```

## Tagging a release

Match upstream's version tag and append `_display`, e.g. if upstream
released `v0.6.1-0`, tag this repo as:

```sh
git tag v0.6.1-0_display
git push origin v0.6.1-0_display
```

Pushing a `v*` tag triggers `.github/workflows/build-lcd.yml`, which
builds the firmware, renames the output to
`esp32-arduino.s3.merged-lcd-<tag>.bin`, and attaches it to an
auto-created GitHub Release for that tag.

## Local build (WSL/Ubuntu)

```sh
sed -i 's/\r$//' *.sh Makefile
chmod +x *.sh prepare-data.sh
git submodule update --init --recursive
./install-deps.sh
make s3-lcd
```

Output: `build/lcd/s3/esp32-arduino.s3.merged.bin`
