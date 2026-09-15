# Compiling piHPSDR

This file documents how to build a working `pihpsdr` binary from source and where the build artefacts are created.

## Scope

piHPSDR builds on Linux and macOS.

- Linux: native build supported
- macOS: native build supported

## Important build facts

- The main build is driven by the root [Makefile](Makefile).
- A normal `make` builds the executable `pihpsdr` in the repository root.
- `make release` copies that executable to `release/pihpsdr/pihpsdr` and creates a release tarball.
- `make app` is for macOS bundle creation and produces `pihpsdr.app`.
- [LINUX/libinstall.sh](LINUX/libinstall.sh) and [MacOS/libinstall.sh](MacOS/libinstall.sh) install dependencies and helper assets, but they do not build the main application binary.

If you ran one of the `libinstall.sh` scripts and expected a runnable binary to appear automatically, you still need to run `make` afterwards.

## Default build behaviour

The root [Makefile](Makefile) detects the host OS and applies these defaults before reading `make.config.pihpsdr`:

| Setting | Linux default | macOS default | Notes |
| --- | --- | --- | --- |
| `GPIO` | `ON` | forced `OFF` | GPIO support is intended for Raspberry Pi style hardware and needs `libgpiod` and `i2c` support. |
| `MIDI` | `ON` | `ON` | Linux uses ALSA MIDI; macOS uses CoreMIDI. |
| `USBOZY` | `OFF` | `OFF` | Enable only if you need legacy USB OZY radio support. |
| `SOAPYSDR` | `OFF` | `OFF` | Enable only if you want SoapySDR radio support and the matching libraries are installed. |
| `PORTFORWARD` | `ON` | `ON` | Uses `miniupnpc`. |
| `TCI` | `ON` | `ON` | Uses `libwebsockets`. |
| `AUDIO` | `PULSE` | `PORTAUDIO` | On macOS, `AUDIO=ALSA` is rewritten to `PORTAUDIO`. |

### Effective macOS overrides

On macOS the Makefile forces two behaviours regardless of what you request:

- `GPIO=OFF`
- `AUDIO=ALSA` becomes `AUDIO=PORTAUDIO`

So if you put `GPIO=ON` or `AUDIO=ALSA` in `make.config.pihpsdr` on macOS, the effective build will still be `GPIO=OFF` and `AUDIO=PORTAUDIO`.

## Prerequisites

### Linux packages

For the default Linux build, install at least:

```bash
sudo apt-get update
sudo apt-get install -y \
  build-essential make gcc g++ gfortran git pkg-config cmake \
  libfftw3-dev libgtk-3-dev libasound2-dev libssl-dev \
  libcurl4-openssl-dev libusb-1.0-0-dev libi2c-dev libgpiod-dev \
  libpulse-dev libpcap-dev libopus-dev libminiupnpc-dev \
  libsqlite3-dev libwebsockets-dev zlib1g-dev libzstd-dev
```

Optional helper:

- [LINUX/libinstall.sh](LINUX/libinstall.sh) installs a fuller Linux dependency set, fonts, desktop launcher assets, and SoapySDR-related components.
- Even after running it, you still need to run `make` to build `pihpsdr`.

### macOS packages

The repository's macOS helper uses Homebrew and installs the packages needed for a normal build. The core set is:

```bash
brew update
brew install \
  gtk+3 librsvg pkg-config portaudio fftw libusb \
  openssl@3 opus miniupnpc libwebsockets zlib \
  cmake makedepend cppcheck python-setuptools
```

Optional helper:

- [MacOS/libinstall.sh](MacOS/libinstall.sh) prepares Homebrew, adjusts environment variables, and installs a broader dependency set including SoapySDR components.
- It does not build the main `pihpsdr` executable.

## Building the binary

From the repository root:

```bash
make
```

The resulting binary is:

```text
./pihpsdr
```

For a clean rebuild:

```bash
make clean
make
```

For a faster parallel build on Linux:

```bash
make -j"$(nproc)"
```

On macOS:

```bash
make -j"$(sysctl -n hw.ncpu)"
```

## Verifying the build

After a successful build, verify that the executable exists in the repository root:

```bash
ls -l pihpsdr
```

If you need the release payload to contain the executable too, run:

```bash
make release
```

That copies the binary to:

```text
release/pihpsdr/pihpsdr
```

## Using make.config.pihpsdr

Do not edit the root [Makefile](Makefile) for routine build configuration.

Instead, create a file named `make.config.pihpsdr` in the repository root, alongside the Makefile. It is included automatically by the Makefile.

Example:

```make
GPIO=OFF
AUDIO=ALSA
```

### Supported user-facing settings

The settings intended for routine customization are:

- `GPIO=ON` or `GPIO=OFF`
- `MIDI=ON` or `MIDI=OFF`
- `USBOZY=ON` or `USBOZY=OFF`
- `SOAPYSDR=ON` or `SOAPYSDR=OFF`
- `PORTFORWARD=ON` or `PORTFORWARD=OFF`
- `TCI=ON` or `TCI=OFF`
- `AUDIO=PULSE`, `AUDIO=ALSA`, or `AUDIO=PORTAUDIO`

### Practical configuration examples

Desktop Linux without GPIO hardware:

```make
GPIO=OFF
```

Linux using ALSA instead of PulseAudio:

```make
GPIO=OFF
AUDIO=ALSA
```

Linux with SoapySDR radio support enabled:

```make
GPIO=OFF
SOAPYSDR=ON
```

Raspberry Pi style Linux build:

```make
GPIO=ON
AUDIO=PULSE
```

macOS build with explicit PortAudio selection:

```make
AUDIO=PORTAUDIO
SOAPYSDR=OFF
```

### Notes on option dependencies

- `GPIO=ON` needs `libgpiod` and `libi2c` support.
- `MIDI=ON` needs ALSA MIDI on Linux and CoreMIDI on macOS.
- `USBOZY=ON` needs `libusb-1.0`.
- `SOAPYSDR=ON` needs SoapySDR libraries and matching radio support modules.
- `PORTFORWARD=ON` needs `miniupnpc`.
- `TCI=ON` needs `libwebsockets`.
- `AUDIO=PORTAUDIO` needs PortAudio.
- `AUDIO=PULSE` needs PulseAudio development libraries.
- `AUDIO=ALSA` needs ALSA development libraries.

## SoapySDR builds

`SOAPYSDR` is off by default on all platforms.

If you enable it:

- Linux expects SoapySDR headers in `/usr/local/include` and libraries in `/usr/local/lib`.
- macOS uses `pkg-config` to locate Homebrew-installed SoapySDR.

The repository helper scripts install or build SoapySDR components because distro or Homebrew packages can be incomplete or API-incompatible for some radios.

If you do not need SoapySDR-backed radios, leave `SOAPYSDR=OFF`.

## Other useful make targets

- `make clean`: remove object files, the main binary, helper binaries, and intermediate Soapy checkout directories
- `make cppcheck`: run static analysis
- `make release`: copy the built binary into `release/pihpsdr` and create the release tarball
- `make app`: macOS-only app bundle creation
- `make bootloader`: build the separate `bootloader` utility
- `make hpsdrsim`: build the simulator utility

## Common pitfalls

### 1. Running libinstall and expecting a binary

`libinstall.sh` scripts prepare the machine. They do not replace running `make`.

### 2. Looking in the wrong directory

After plain `make`, the binary is created in the repository root, not in `release/pihpsdr`.

### 3. Enabling GPIO on a normal desktop Linux machine

`GPIO=ON` is the Linux default in the Makefile. If you are building on a desktop or laptop and do not need GPIO controller support, setting `GPIO=OFF` is often the simplest choice.

### 4. Enabling SoapySDR without installing its libraries

If `SOAPYSDR=ON`, ensure the SoapySDR core library and any needed radio modules are installed first.

## Recommended workflows

### Linux desktop or Raspberry Pi

1. Install dependencies directly, or run [LINUX/libinstall.sh](LINUX/libinstall.sh).
2. Create `make.config.pihpsdr` if you need non-default options.
3. Run `make`.
4. Confirm `./pihpsdr` exists.

### macOS

1. Install Homebrew dependencies directly, or run [MacOS/libinstall.sh](MacOS/libinstall.sh).
2. Create `make.config.pihpsdr` if needed.
3. Run `make` or `make app`.
4. Confirm `./pihpsdr` or `./pihpsdr.app` exists.
