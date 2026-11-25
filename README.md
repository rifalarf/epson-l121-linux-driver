# Epson L121 / L120 Series — Unlocked Linux Driver & Maintenance Suite

Native Linux CUPS driver, official ICC color profile, unlocked PPD, and USB maintenance utility (`l121ctl`) for **Epson L121 / L120 Series** (`04b8:005e`, board `FY13`).

## What's Unlocked vs Standard Linux Drivers
- **True 720×720 DPI VSD & Official ICC Profile:** Uses Epson's native `libEpson_201310w.so` halftone engine + `Epson IJ Printer 07.icc` extracted from the Windows x64 v2.60 driver.
- **Full Mechanical Paper Sizes:** Unlocks **F4 / Folio (215×330 mm)**, B5, A5, A6, 4R, 5R, Envelopes, **Banner sizes up to 210×1117.6 mm**, and Custom sizes (`89×127 mm` to `215.9×1117.6 mm`), while removing erroneous A2/A3/B3 entries from Epson's stock Linux PPD.
- **Production Print Features:** `MirrorImage` (sublimation/transfer), `Rotate180`, `PosterPrinting` (2×1 to 4×4), `AdobeRGB` & `EPSONVivid` color modes, and watermarks.
- **Native USB Maintenance (`l121ctl`):**
  - `l121ctl status` — Live IEEE-1284 ID & `@BDC ST2` telemetry
  - `l121ctl nozzle-check` — Nozzle check pattern + FW version, Serial & Page Counter
  - `l121ctl clean` / `power-clean` — Standard head cleaning & Power Ink Flushing
  - `l121ctl roller-clean` — Paper feed roller cleaning
  - `l121ctl align` — Head alignment print & EEPROM write (`DD` + `SV`)

## Build & Install (Arch / CachyOS)

```bash
makepkg -si
sudo udevadm control --reload-rules && sudo udevadm trigger -s usb
sudo lpadmin -p L120-Series -P /usr/share/cups/model/epson-l121/EPSON_L121_Unlocked.ppd -E
```

## Technical Notes
- Epson L121 and L120 share identical USB VID/PID (`04b8:005e`), `FY13` firmware (`RA27KA`), and LUT tables (`E_WSL0UE.DLL` / `Epson_201310w.1.data`).
- Full 0 mm borderless printing is intentionally not enabled because the L120/L121 platen lacks full-width edge ink-absorbing pads.
