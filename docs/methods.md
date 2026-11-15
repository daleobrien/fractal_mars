# Speed-up methods

[Back to the README](../README.md)

Fractal coding spends almost all its time finding, for each range block, the
domain block that best matches it. Mars was written to compare six ways of
cutting that search down, each written as a plug-in so a new one only needs
two functions.

| Flag | Method | How it narrows the search |
| ---- | ------ | ------------------------- |
| `-F` | Fisher | Classifies blocks by the order of their quadrants' brightness and variance, and only compares blocks in the same class |
| `-X` | Hurtgen | Classifies by which quadrants are brighter than the block's mean (15 classes), then by Fisher's variance order: 360 classes |
| `-C` | Mass centre | A feature vector from the block's centre of brightness, searched with a kd-tree (the default) |
| `-S` | Saupe | A feature vector from the block shrunk down, searched with a kd-tree for its nearest neighbours |
| `-Z` | Saupe-Fisher | Saupe's search, with Fisher's way of working out the isometry |
| `-Y` | Mass centre-Saupe | The two feature vectors combined; use a small `-n` |

## On Lena

Encoding the 512×512 `lena.raw` with each method and its default settings,
then decoding with `decmars`:

| Method | Encode | `.ifs` size | Ratio | PSNR |
| ------ | -----: | ----------: | ----: | ---: |
| Fisher (`-F`) | 0.23 s | 16,504 bytes | 15.9:1 | 33.28 dB |
| Hurtgen (`-X`) | 0.47 s | 16,122 bytes | 16.3:1 | 33.39 dB |
| Mass centre (`-C`) | 0.39 s | 15,814 bytes | 16.6:1 | 33.46 dB |
| Saupe (`-S`) | 1.58 s | 14,773 bytes | 17.7:1 | 33.84 dB |
| Saupe-Fisher (`-Z`) | 0.35 s | 15,188 bytes | 17.3:1 | 33.75 dB |
| Mass centre-Saupe (`-Y`) | 0.15 s | 17,789 bytes | 14.7:1 | 32.39 dB |

Saupe-Fisher gives nearly the best quality and size for a quarter of Saupe's
time. Timings are from one run on a current Linux machine, so only their
order matters.

## Splitting

The quadtree is split on RMS error by default. Polvere also implemented
entropy (`-e`) and variance (`-v`) split decisions, and thresholds that change
from level to level (`-a`); the manual suggests factors of about 2.0 for RMS
and 4.0 for variance, and notes that adaptive thresholds make the result less
blocky.

## References

The papers behind each method are listed at the end of
[MANUAL.txt](../MANUAL.txt).
