LIST OF RECENT PIHPSDR MATRIX CHANGES
=====================================

September 2026
---------------

### v3.0.15 - released 2026-09-17

- 2026-09-17: Added GitHub issue templates for bug reports and feature
  requests.

- 2026-09-17: Switched to archived release metadata and next-patch development
  versioning for source and post-tag builds.

- 2026-09-16: Fixed the false-positive "G2 Panel" menu item on Hermes Lite 2
  (HL2) builds by preventing the G2-v2 detection flag from being forced true
  when GPIO support is enabled without a real G2-v2 panel being detected.

### v3.0.14 - released 2026-09-15

- 2026-09-15: Added a GitHub release workflow for automated Matrix releases.

- 2026-09-15: Added a Matrix build ID to GIT_VERSION, amended the Makefile
  GIT_VERSION handling and DEPEND section, and added README_COMPILING.md.

- 2026-09-15: Cherry-picked from upstream (DL1YCF) - updated the Linux
  libinstall SoapySDR install procedure (original 2026-08-06).

- 2026-09-15: Cherry-picked from upstream (DL1YCF) - reworked the macOS
  libinstall to build all SoapySDR components from source, since pothosware is
  now "untrusted" by Homebrew (original 2026-08-04).

- 2026-09-15: Cherry-picked from upstream (DL1YCF) - added a short "hold" to the
  meter peak-and-hold behaviour (original 2026-06-26).

- 2026-09-15: Cherry-picked from upstream (DL1YCF) - small updates to the DX
  cluster popup, server menu, and SoapySDR/STEMlab discovery (original
  2026-06-26).

- 2026-09-13: New feature - added JSON-backed configuration for
  panadapter/waterfall colour themes, the VFO panel layout, and the 60m band
  plan, loaded at start-up and re-readable at runtime, with compiled-in
  defaults retained.

- 2026-09-13: New feature - added a persisted menu toggle to enable/disable the
  panadapter up/down (noise-floor) drag behaviour.

- 2026-09-13: Kept user properties across a PROPERTY_VERSION mismatch, wrote a
  timestamped backup of the original file, and warned the operator instead of
  discarding settings.

- 2026-09-13: Kept RX2 (VFO B) panadapter high/low/step settings independent by
  only applying per-band panadapter values to RX1.

- 2026-09-13: Centred a freshly-enabled manual notch in the current RX
  pass-band instead of leaving it at 0 Hz.

- 2026-09-13: Fixed XIT +/- so it now uses the same step size as RIT instead of
  a fixed 100 Hz increment.

- 2026-09-13: Made the Linux libinstall PipeWire/PulseAudio check safe for
  desktop installations by only removing pulseaudio when pipewire-pulse is
  actually installed and the removal is clean.

### v3.0 - released 2026-06-25

- Baseline upstream piHPSDR v3.0 release from which the Matrix line is derived.
