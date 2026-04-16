# Motorola Boot Logo

Script to encode, decode Motorola boot logo binary file. Extend [vedant-y/moto-bootlogo](https://github.com/vedant-y/moto-bootlogo) with new features.

## Requirements
- [Python 3](https://python.org)
- [Pillow](https://python-pillow.org)

## Usage

- Decoding/Extracting
```
$ moto-bootlogo.py -i logo.bin -o images
```

- Encoding/Compressing
```
$ moto-bootlogo.py -i images -o logo.bin
```

- List images, no decoding
```
$ moto-bootlogo.py -l logo.bin
```

## Options

- `--no-dedup` — Disable image deduplication (by default, identical encoded images share offsets to shrink output).

- `--pad SIZE` — Pad output file with `0x00` up to SIZE. Accepts `32MB`, `32MiB`, or raw bytes (e.g. `33554432`).

- `--replace COLOR=NAME [COLOR=NAME ...]` — Replace one or more images with a solid color of the original image's dimensions. COLOR can be a named color (`black`, `white`, `red`, `green`, `blue`, `yellow`, `cyan`, `magenta`, `orange`, `gray`) or a hex code (`#RRGGBB` or `RRGGBB`). NAME is the image name (without `.png`).

  ```
  $ moto-bootlogo.py -i images -o logo.bin --replace black=logo_boot white=logo_unlocked
  $ moto-bootlogo.py -i images -o logo.bin --replace "#FF8800=logo_boot"
  ```

- Missing `.png` files listed in `data.json` are automatically removed from the output metadata during encoding.

## File-to-file transform

Apply `--replace` and/or `--pad` directly to an existing `.bin` without manually extracting first. The output path must look like a file (e.g. end with `.bin` / `.img`):

```
$ moto-bootlogo.py -i logo.bin -o logo_new.bin --replace black=logo_boot --pad 32MiB
```

This decodes to a temporary directory, applies the modifications, and re-encodes.

---

## Experiment on revising Motorola Edge 2024's boot logo

* Device: Motorola Edge 2024
* Codename: avatrn
* Model: XT2405-1
* Bootloader: Unlocked
* Boot Logo Binary File Size: 32 MiB (33,554,432 Bytes)

After unlocking the bootloader, the device displays an ugly warning page during startup. The boot sequence proceeds as follows: boot logo → warning page → boot logo → boot animation (if applicable).

The original goal was to replace the default warning page with Motorola's boot logo, so that it would not be as striking during page transitions. However, testing revealed that the warning page is cryptographically signed. Any modification causes the device to reject the custom page and fall back to another ugly warning screen with black background with orange text.

The good news is, at least this fallback warning is not as conspicuous as the factory default. Additionally, the boot logo itself is not protected. This led to the final approach: replace the boot logo with a solid black image, so that although the warning page still appears, it is not as striking as the original one.

### Revising the boot logo binary

Although `logo_a.bin` and `logo_b.bin` extracted from the device's A/B slots differ in hash value, the revised output is identical across both. I don't know why, but this is good news since it conveniently allows a single modified file to be flashed to both slots.

Unpack the binary:

```sh
python moto-bootlogo.py -i logo_a.bin -o logo_images
```

Delete `orange1.png` and `orange2.png`, then generate the modified binary:

```sh
python moto-bootlogo.py -i logo_images -o logo_no_orange_black_boot.bin --replace black=logo_boot --pad 32MiB
```
