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

### Reference
- [aboot](https://github.com/grub4android/lk/blob/master-uboot/app/aboot/aboot.c#L2710 "LK embedded kernel - aboot.c")
- [MotoBootLogoMaker](https://github.com/CaitSith2/MotoBootLogoMaker "CaitSith2 - MotoBootLogoMaker")
- [XDA Developers](https://forum.xda-developers.com/showpost.php?p=48859155&postcount=136 "Carock's XDA Post")
