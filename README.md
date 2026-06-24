# ncSenderProOS — Releases

Pre-built, ready-to-flash device images for [ncSender Pro](https://github.com/siganberg/ncsenderpro.releases) — a complete kiosk OS that boots straight into the ncSender CNC controller UI.

Pick the image that matches your hardware, flash it to a micro-SD card, plug it in, and you're done.

---

## Supported Hardware

| Device | Image asset | Notes |
|--------|------------|-------|
| **Raspberry Pi 5** | `ncSenderOS-pi5-v*.img.xz` | 4 GB or 8 GB |

---

## What You'll Need

- A **micro-SD card** (16 GB or larger, Class 10 / U1 minimum, U3 / A2 recommended)
- A **micro-SD card reader** for your computer
- The **latest release image** from this repository
- [**balenaEtcher**](https://etcher.balena.io/) — free, cross-platform, handles `.img.xz` automatically

---

## Step 1 — Download the Image

1. Go to the [Releases](https://github.com/siganberg/ncSenderProOs.releases/releases) page.
2. Open the latest release.
3. Download the `.img.xz` asset for your device, e.g.
   `ncSenderOS-pi5-v2.0.3.img.xz`.

The file is xz-compressed; balenaEtcher decompresses it on the fly when flashing — no need to unpack it yourself.

---

## Step 2 — Flash the Card with balenaEtcher

1. Open **balenaEtcher**.
2. Click **Flash from file** and pick the `.img.xz` you just downloaded.
3. Click **Select target** and pick your micro-SD card.
   *(Triple-check this. The selected drive will be completely erased.)*
4. Click **Flash!** and enter your password when prompted.
5. Wait for "Flash Complete" (5–10 minutes). Eject when finished.

---

## Step 3 — First Boot

1. Eject the micro-SD card from your computer and insert it into your Pi 5.
2. Connect:
   - **HDMI** → your monitor or CNC touchscreen
   - **Ethernet** (recommended) or set up WiFi later
   - **USB** → your CNC controller (grblHAL / FluidNC)
   - **Power** (5V/5A USB-C for Pi 5)
3. The Pi will boot. You'll see the **FrancisCreation splash logo**, then ncSender will launch automatically into kiosk mode.
4. First boot expands the filesystem and may take **1–2 minutes** longer than subsequent boots.

---

## After First Boot

- **Connect to your controller**: ncSender auto-detects USB CNC controllers. If yours isn't connected yet, the header will show "Connecting…" until it finds one.
- **WiFi setup**: tap **Settings → Network → WiFi** in the ncSender UI.
- **Screen rotation**: **Settings → Display → Rotation** (Portrait modes supported; touch axes follow automatically).
- **Updates**: ncSender checks for updates on launch. New versions install in-place from the UI — the device reboots once and comes back on the new version.

---

## Drop G-code Files from Your Computer (SMB)

The image exposes its G-code folder as a guest-accessible SMB share so you can save files to it from your CAM software without copying via USB.

- **Share path:** `smb://ncsender/ncSender`
  *(or `smb://<device-ip>/ncSender` if your network doesn't resolve the hostname)*
- **Auth:** none — guest access, read/write
- **On the Pi:** maps to `/root/.config/ncSender/` (G-code goes in the `gcode-files/` subfolder)

**macOS:** Finder → ⌘K → paste the URL above.
**Windows:** File Explorer → address bar → `\\ncsender\ncSender` (or `\\<device-ip>\ncSender`).

---

## Security & Hardening

> **These images are designed to run on a trusted internal network** (your shop LAN, behind your router/firewall). The default credentials and open guest SMB are intentionally weak so the device works out of the box on day one. **Do not expose the Pi directly to the public internet.**

Defaults:

| Service | Default | Purpose |
|---|---|---|
| **SSH** | enabled, `root` / `ncsender`, root login allowed | troubleshooting + recovery |
| **SMB (Samba)** | enabled, guest read/write on share `ncSender` | drop G-code from CAM software |

### Change the root password
```
ssh root@<device-ip>
passwd
```
Recommended for any device that lives on a shared / untrusted LAN.

### Disable SSH (lock down remote shell)
```
ssh root@<device-ip>
systemctl disable --now ssh
```
You will lose remote access — only do this if you can reach the Pi physically (keyboard + monitor) or via the ncSender UI for everything you need.

### Disable SMB (turn off the file share)
```
ssh root@<device-ip>
systemctl disable --now smbd nmbd
```
You can still load G-code from a USB stick or from the ncSender UI's file picker.

### Re-enable later
```
systemctl enable --now ssh         # or: smbd nmbd
```

---

## Troubleshooting

| Symptom | Likely fix |
|--------|-----------|
| Black screen, no boot logo | Reseat the SD card. If it persists, the card may be slow or counterfeit — try another. |
| Boots to text terminal instead of ncSender | Wait ~30 s; first boot does extra work. If it stays at a terminal, see `journalctl -u ncsender-kiosk` over SSH. |
| ncSender UI shows "no controller" | Check USB cable + controller power. Try a different USB port. |
| OTA update never completes | Power-cycle once. The supervisor will recover. |
| WiFi disconnects after a while | `Settings → Network → WiFi` and toggle "Power save" off. |

For anything else, [open an issue](https://github.com/siganberg/ncSenderProOs.releases/issues) with the device model, image version, and a description of the symptom.

---

## Disclaimer — No Warranty, Use at Your Own Risk

These images are provided **as-is, without warranty of any kind**, express or implied — including but not limited to warranties of merchantability, fitness for a particular purpose, accuracy, or non-infringement.

You alone are responsible for **how you use this software and what it controls**. The authors, contributors, and FrancisCreation accept **no liability** for any damage or loss — including but not limited to damage to your CNC machine, workpiece, tooling, computer, network, data, property, or for personal injury — arising from the installation, configuration, or operation of ncSenderProOS or any bundled component.

CNC machinery can cause **serious injury and costly damage** when misused. Before running any job:

- Verify your G-code, feed rates, spindle speed, and tool offsets
- Keep your **e-stop within reach** at all times
- Do an **air-cut / dry run** before cutting real material
- **Never leave a running job unattended**

By flashing and running this image you acknowledge that you accept these terms and that you are operating the device at your own risk.

---

## License

ncSenderProOS images bundle:
- **ncSender Pro** — proprietary. The source repo is private; for license terms, downloads, and issue reporting see the public counterpart at <https://github.com/siganberg/ncsenderpro.releases>.
- **Raspberry Pi OS** base — Debian (various open-source licenses)
- **pi-gen** build framework — Raspberry Pi Foundation

Each component retains its original license. The image bundle as a whole is distributed for use with licensed ncSender Pro installations.
