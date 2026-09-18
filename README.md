# Tania's Sound Box

A Linux sound-control project derived from FxSound. Initial target: **Linux Mint
22.3 Zena, Xfce edition, x86_64**. PipeWire is the Linux audio backend.

## Current status

Version **0.19.0** supports live stereo master gain, balance, nine-band EQ,
bypass, a native stereo-linked compressor and output limiter, portable presets,
selected-output recovery, and exportable diagnostics. PipeWire handles routing and
EQ; dynamics are supplied as bundled native LADSPA plugins. The named recording
monitor carries the processed mix for OBS. Changes are applied explicitly with
**Apply controls**, including checkbox changes; saving settings keeps them for
the next launch. The panel distinguishes saved, pending and confirmed controls.

The **Mic configurator** adds an independent processed microphone for OBS, channel
mixing, gain/EQ, high-pass filtering, a noise gate, de-essing, compression, limiting,
mute, live meters, test recordings, room-noise calibration, and microphone presets.
**Capture Point** lists devices and active apps with explicit volume, mute, default,
port, profile and routing controls. See [microphone guide](docs/MICROPHONE.md).

The native C++ DSP library remains an offline-tested reference. Live processing
uses PipeWire builtin filters rather than the inherited Windows DSP utilities.
Output limiting defaults to a -1 dBFS sample-peak ceiling with 100 ms release.
Live parameter changes use small steps; the panel checks actual routing links.
The compressor defaults to off. Lookahead, true-peak limiting, and
sample-accurate control ramps remain future work. Full graph/server recovery
recreates node IDs; OBS reconnection still needs testing in OBS.

The original Windows application, DSP, driver integration, and installer remain
in `fxsound/`, `dsp/`, `audiopassthru/`, and `Installer/` as porting sources.
They are excluded from the Linux build.

The desktop interface uses persistent workspace navigation, grouped effect pages,
vertical EQ faders and separate device/job/app-routing views. The shared navy/amber
palette can be revised independently in `linux/ui/ui_design.py`.

## Build on Linux Mint

```sh
sudo apt install build-essential cmake ninja-build pkg-config ladspa-sdk python3 python3-tk pipewire-bin pulseaudio-utils
sh linux/scripts/check-environment.sh
cmake -S . -B build -G Ninja -DCMAKE_BUILD_TYPE=Release
cmake --build build
ctest --test-dir build --output-on-failure
./build/tanias-sound-box
```

Launch without arguments to open the panel. Use `--paths` to inspect file
locations, `--audio-status` for output-device JSON, `--diagnostics` for a support
report, `--check` for runtime readiness, or `--version` to check the version. Live processing uses the installed PipeWire runtime and does not require
audio development headers.

Create a Debian package with:

```sh
cpack --config build/CPackConfig.cmake -G DEB
```

See [Linux porting notes](docs/LINUX_PORT.md) for the remaining platform work.

## Attribution and license

Derived from [FxSound](https://github.com/fxsound2/fxsound-app), copyright
FxSound LLC and contributors. Original copyright notices are retained.
The project retains the [GNU AGPL v3 license](LICENSE). Reused JUCE components
remain subject to their upstream licensing requirements.

## Test the OBS devices

Launch the panel, select **Hardware Output**, then click **Enable Sound Box**.
In your desktop sound settings or an application's output selector, send audio
to **Tania's Sound Box**. In OBS, add an Audio Input Capture source and select
**Tania's Sound Box - Recording Monitor**. This carries the processed Sound Box playback mix, not your microphone.
Adjust gain, balance, or EQ and click **Apply controls**. **Bypass processing**
neutralizes gain, balance, EQ, compression, and limiting while preserving playback and OBS
capture. Dynamics controls are applied with the other
controls. The limiter protects the mix before the recording monitor and hardware output.

The app does not set playback or microphone defaults. Linux may automatically
restore your previously chosen Sound Box defaults when the devices reappear.
Only apps routed to Sound Box appear in its recording monitor. Keep the panel
open during recording. **Disable Sound Box** or closing the app removes its
virtual devices. Select your normal hardware output when finishing the test.
Use one OBS capture of this mix to avoid recording it twice via Desktop Audio.

## Live backend verification

The normal CTest suite does not create audio nodes. An optional signal test
creates isolated temporary nodes and measures live gain, balance, EQ, compression, limiter behavior, and bypass
at the processed monitor, without hardware playback or microphone capture:

```sh
python3 linux/tests/test_live_audio.py
```

## Playback logic candidate 0.14.0

Milestone packages through 0.14.0 are in `build/packages/`. Use the latest:

```sh
sudo apt install ./build/packages/tanias-sound-box-0.14.0-Linux.deb
tanias-sound-box
```

Or run `./build/tanias-sound-box` directly from this checkout. See [build history](docs/BUILD_HISTORY.md) and the
[user validation guide](docs/VALIDATION.md). This completes the planned playback
logic implementation pass; real-device and OBS acceptance testing remain.

Presets include Flat, Voice clarity and Warm music. Save as creates a custom
preset; Import/Export use schema-1 JSON. Loading stages controls until Apply.
Presets and settings live under the XDG configuration directory, usually
`~/.config/tanias-sound-box/`, in `presets.json` and `settings.json`. Controls and
output preference are saved atomically together. Old `preferences.json` is read
when migrating legacy settings.
The selected output is remembered when settings are saved or the app closes.
Enable is required each launch. If the selected device disappears, the session
waits for it and resumes with the last applied controls when it returns.
Disable cancels recovery. No audio-server service restart is required.

Export diagnostics saves a report of runtime tools, bundled plugins, devices,
route health and session state. The CLI report reads the current server state.
Package verification extracts to a temporary directory, without installation:

```sh
python3 linux/scripts/verify-package.py build/packages/tanias-sound-box-0.14.0-Linux.deb --gui
```

Optional hardware-route recovery check, with no test tone or microphone capture:

```sh
python3 linux/tests/test_live_recovery.py
```
