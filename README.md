# ncSenderProOS — Releases

Pre-built, ready-to-flash device images for [ncSender Pro](https://github.com/siganberg/ncSenderPro) — a complete kiosk OS that boots straight into the ncSender CNC controller UI.

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

- **Connect to your controller**: ncSender auto-detects USB CNC controllers. If yours doesn't show up, check the bottom-status bar for "No controller detected".
- **WiFi setup**: tap **Settings → Network → WiFi** in the ncSender UI.
- **Screen rotation**: **Settings → Display → Rotation** (Portrait modes supported; touch axes follow automatically).
- **Updates**: ncSender checks for updates on launch. New versions install in-place from the UI — the device reboots once and comes back on the new version.

---

## SSH Access (optional, advanced)

SSH is enabled by default on the released images. Connect with:
```
ssh root@<device-ip>
```
Default root password: **`ncsender`**

> **Change this immediately** if your device is on a network you don't fully trust:
> ```
> ssh root@<device-ip>
> passwd
> ```

---

## Troubleshooting

| Symptom | Likely fix |
|--------|-----------|
| Black screen, no boot logo | Reseat the SD card. If it persists, the card may be slow or counterfeit — try another. |
| Boots to text terminal instead of ncSender | Wait ~30 s; first boot does extra work. If it stays at a terminal, see `journalctl -u ncsender-kiosk` over SSH. |
| ncSender UI shows "no controller" | Check USB cable + controller power. Try a different USB port. |
| OTA update never completes | Power-cycle once. The supervisor will recover. |
| WiFi disconnects after a while | `Settings → Network → WiFi` and toggle "Power save" off. |

For anything else, open an issue on the [main ncSenderProOs repo](https://github.com/siganberg/ncSenderProOs/issues) with the device model, image version, and a description of the symptom.

---

## Building Your Own Image

If you want to roll a custom image (different logo, extra packages, etc.) the build scripts and overlays live in the source repo: <https://github.com/siganberg/ncSenderProOs>.

---

## License

ncSenderProOS images bundle:
- **ncSender Pro** — proprietary, see <https://github.com/siganberg/ncSenderPro> for license terms
- **Raspberry Pi OS** base — Debian (various open-source licenses)
- **pi-gen** build framework — Raspberry Pi Foundation

Each component retains its original license. The image bundle as a whole is distributed for use with licensed ncSender Pro installations.
