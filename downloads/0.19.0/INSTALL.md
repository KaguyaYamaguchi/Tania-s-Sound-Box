# Tania's Sound Box 0.19.0

Linux desktop audio controls and microphone processing. Tested on Linux Mint
22.3, x86_64 (amd64), with PipeWire and its PulseAudio compatibility service.
This installer is not for Windows, macOS or ARM computers. Other Linux
configurations have not yet been validated.

## Install

Double-click `tanias-sound-box-0.19.0-Linux.deb` and open it with the Mint package
installer. Alternatively, open a terminal in the download folder and run:

```sh
sudo apt install ./tanias-sound-box-0.19.0-Linux.deb
```

APT installs the listed runtime dependencies. Internet access may be needed for
those dependencies. Launch Tania's Sound Box from the applications menu or run:

```sh
tanias-sound-box
```

Enable playback/microphone processing explicitly and choose your devices.
For OBS, the processed mic is **Tania's Sound Box - Microphone**; desktop audio
uses **Tania's Sound Box - Recording Monitor**. Keep the app running during use.
Use headphones when enabling microphone monitoring.

## Remove

```sh
sudo apt remove tanias-sound-box
```

User settings remain in ~/.config/tanias-sound-box.

## Source and pricing

Payment supports development. This version has no subscription, activation
requirement or expiry. Keep and use your downloaded copy, and share it under
AGPLv3. Payment does not promise lifetime support or future features.

The corresponding Linux source is included in
`tanias-sound-box-0.19.0-source.zip`, with LICENSE, build instructions, native
plugin sources and tests. See its README.md to build. Derived from FxSound;
original copyright and licensing notices are retained. Everyone receives the
same app whether paying or downloading it free.

## Verify files

```sh
sha256sum -c SHA256SUMS
```
