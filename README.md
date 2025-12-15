# wrap-nvidia-complete.sh
Production-grade NVIDIA GPU offloading tool for Linux hybrid graphics systems. Automatically wraps desktop applications to run on the discrete GPU using DRI_PRIME, without proprietary drivers or disabling Secure Boot. XDG-aware, spec-compliant, safe, and reversible.

# NVIDIA PRIME Desktop App Wrapper

Automatically run GUI applications on your **discrete NVIDIA GPU** on Linux **without installing NVIDIA proprietary drivers** and **without disabling Secure Boot**.

This project safely wraps existing `.desktop` launchers so that OpenGL / Vulkan applications are started with the correct environment variables (`DRI_PRIME`, Vulkan ICDs, PRIME hints) to offload rendering to the NVIDIA GPU when possible.

---

## Why this exists

On hybrid GPU systems (Intel/AMD iGPU + NVIDIA dGPU):

* Secure Boot blocks proprietary NVIDIA drivers
* Nouveau + PRIME works, but apps default to the iGPU
* Manually prefixing every app with `DRI_PRIME=1` is impractical

This tool:

* Scans your system’s desktop applications
* Detects which apps are safe to offload
* Creates **separate NVIDIA-prefixed launchers** (non-destructive)
* Works with **Mesa + Nouveau** and **Secure Boot enabled**

No system files are modified.

---

## Key Features

* ✅ Secure Boot compatible
* ✅ No NVIDIA proprietary drivers required
* ✅ XDG-spec compliant (`/usr/share`, `~/.local/share`)
* ✅ Automatic OpenGL **and** Vulkan handling
* ✅ Wayland-aware (skips Wayland-only apps)
* ✅ Safe backup & restore
* ✅ Dry-run, audit, JSON output
* ✅ One-command uninstall

---

## Requirements

* Linux with hybrid GPU (Intel/AMD + NVIDIA)
* Mesa + Nouveau
* `bash`, `coreutils`

Recommended tools (optional but useful):

```bash
sudo apt install mesa-utils vulkan-tools desktop-file-utils
```

---

## Installation

Clone the repository:

```bash
git clone https://github.com/<your-username>/<repo-name>.git
cd <repo-name>
chmod +x wrap-nvidia-complete.sh
```

---

## Basic Usage

### 1. See what will happen (safe)

```bash
./wrap-nvidia-complete.sh --list
```

JSON output (for auditing):

```bash
./wrap-nvidia-complete.sh --list --json
```

---

### 2. Dry run (no files written)

```bash
./wrap-nvidia-complete.sh --dry-run
```

---

### 3. Wrap applications (recommended)

```bash
./wrap-nvidia-complete.sh
```

This will:

* Create wrapped launchers in:

  ```
  ~/.local/share/applications-nvidia/
  ```
* Add `(NVIDIA)` to the app name
* Leave original launchers untouched

Search your app menu for **“(NVIDIA)”**.

---

## Aggressive Mode

By default, some apps (browsers, editors, office apps) are skipped.

To include them:

```bash
./wrap-nvidia-complete.sh --aggressive
```

Use this only if you know the app benefits from GPU acceleration.

---

## Wrapper Script

A single wrapper is created at:

```bash
~/.local/bin/nvidia-offload
```

It sets:

* `DRI_PRIME=1`
* NVIDIA / Nouveau Vulkan ICDs
* PRIME render hints

### Enable wrapper debugging

```bash
./wrap-nvidia-complete.sh --wrapper-logging
```

Runtime logs will appear in:

```bash
/tmp/nvidia-offload-<pid>.log
```

---

## Verification

Create and run a verification helper:

```bash
./wrap-nvidia-complete.sh --verify
```

Expected output:

```text
OpenGL renderer: NV136
```

This confirms GPU offloading is active.

---

## Restore from Backup

If you re-run the script, existing wrapped apps are backed up automatically.

To restore:

```bash
./wrap-nvidia-complete.sh --restore --backup-dir <backup-path>
```

---

## Uninstall (Clean Removal)

```bash
./wrap-nvidia-complete.sh --uninstall
```

Removes:

* Wrapped desktop entries
* Wrapper script
* Verification helper

Backups are preserved.

---

## Safety & Design Notes

* Original `.desktop` files are never modified
* Duplicate wrapping is prevented
* Fragile launchers (`sh -c`, quoted Execs) are skipped
* Steam is intentionally excluded (manages GPU per-game)
* Flatpak & AppImage apps are skipped (sandboxed)

---

## Known Limitations

* Wayland-native apps may ignore PRIME
* Some Vulkan apps select devices internally
* Flatpak requires portal-based GPU permissions

---

## When should you use this?

✔ Laptop with NVIDIA dGPU
✔ Secure Boot enabled
✔ Nouveau driver
✔ Want per-app GPU control

❌ Desktop with only NVIDIA
❌ Proprietary NVIDIA driver users

---

## License

MIT License

---

## Author

Built for real-world Secure Boot + Nouveau systems.
Designed to be **auditable, reversible, and safe by default**.

If you want improvements, open an issue or PR.
