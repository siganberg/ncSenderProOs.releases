# ncSenderProOS — Releases

Pre-built, ready-to-flash device images for [ncSender Pro](https://github.com/siganberg/ncSenderPro) — a complete kiosk OS that boots straight into the ncSender CNC controller UI.

Pick the image that matches your hardware, flash it to a micro-SD card, plug it in, and you're done.

---

## Supported Hardware

| Device | Image asset | Notes |
|--------|------------|-------|
| **Raspberry Pi 5** | `ncSenderOS-pi5-v*.img.xz` | 4 GB or 8 GB |

Looking for **Pi 4 / Dragon Q6A / other** support? It's on the roadmap — open an issue if you'd like it prioritized.

---

## What You'll Need

- A **micro-SD card** (16 GB or larger, Class 10 / U1 minimum, U3 / A2 recommended)
- A **micro-SD card reader** for your computer
- The **latest release image** from this repository
- A flashing tool (any one of these):
  - [Raspberry Pi Imager](https://www.raspberrypi.com/software/) — **recommended for beginners**, handles `.img.xz` automatically
  - [balenaEtcher](https://etcher.balena.io/) — cross-platform GUI, handles `.img.xz` automatically
  - Command-line `dd` / `xzcat` — for advanced users

---

## Step 1 — Download the Image

1. Go to the [Releases](https://github.com/siganberg/ncSenderProOs.releases/releases) page.
2. Open the latest release.
3. Download the `.img.xz` asset for your device, e.g.
   `ncSenderOS-pi5-v2.0.3.img.xz`.

The file is xz-compressed; both Raspberry Pi Imager and balenaEtcher decompress it on the fly when flashing — no need to unpack it yourself.

---

## Step 2 — Flash the Card

### Option A — Raspberry Pi Imager (easiest)

1. Open **Raspberry Pi Imager**.
2. Click **CHOOSE DEVICE** → pick **Raspberry Pi 5**.
3. Click **CHOOSE OS** → scroll to the bottom → **Use custom** → select the `.img.xz` file you downloaded.
4. Click **CHOOSE STORAGE** → select your micro-SD card.
   *(Triple-check this. The selected drive will be erased.)*
5. Click **NEXT** → when asked about OS customization, click **NO, CLEAR SETTINGS**
   *(ncSenderOS comes pre-configured; the Imager's WiFi/user prompts would be ignored.)*
6. Click **YES** to write. Enter your password when prompted.
7. Wait 5–10 minutes. Eject when finished.

### Option B — balenaEtcher

1. Open **balenaEtcher**.
2. **Flash from file** → pick the `.img.xz`.
3. **Select target** → pick your micro-SD card.
4. **Flash!** Enter your password when prompted.
5. Wait for "Flash Complete". Eject when finished.

### Option C — Command line (macOS / Linux)

> Use this only if you're comfortable with `dd`. A wrong device path will destroy data.

**macOS**:
```bash
# Find the SD card's BSD name (look for size match, often /dev/disk4)
diskutil list external physical

# Unmount the card (replace diskN with the right number)
diskutil unmountDisk /dev/diskN

# Decompress and write (use /dev/rdiskN — the "raw" device — for ~10× speed)
xzcat ~/Downloads/ncSenderOS-pi5-v*.img.xz | sudo dd of=/dev/rdiskN bs=4m status=progress

# Sync and eject
sudo sync
diskutil eject /dev/diskN
```

**Linux**:
```bash
# Find the card (e.g. /dev/sdX)
lsblk

# Unmount any auto-mounted partitions
sudo umount /dev/sdX*

# Write
xzcat ~/Downloads/ncSenderOS-pi5-v*.img.xz | sudo dd of=/dev/sdX bs=4M status=progress conv=fsync
sudo eject /dev/sdX
```

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
