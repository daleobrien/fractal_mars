# Encoding and decoding

[Back to the README](../README.md)

`make` builds two programs, `encmars` and `decmars`, with gcc. Both take
`[-options] [inputfile [outputfile]]`, and `-h` lists every option with its
default. [MANUAL.txt](../MANUAL.txt) is the author's full description.

## Encoding

```bash
./encmars                       # lena.raw -> lena.ifs
./encmars -Q photo.pgm photo.ifs
./encmars -W 256 -H 256 small.raw small.ifs
```

Input is greyscale, either raw bytes (512×512 unless `-W` and `-H` say
otherwise) or PGM.

| Option | Default | What it does |
| ------ | ------- | ------------ |
| `-F` `-X` `-C` `-S` `-Z` `-Y` | `-C` | The speed-up method: see [Speed-up methods](methods.md) |
| `-r #` | 8.0 | RMS error above which a range block is split |
| `-e #`, `-v #` | off | Split on entropy or variance instead (set `-r` high to turn RMS off) |
| `-a #` | 1.0 | Adaptive threshold factor: how the split threshold changes per quadtree level |
| `-m #`, `-M #` | 4, 16 | Smallest and largest range block |
| `-d #` | 4 | Step between domain blocks in the codebook (even values only) |
| `-A #`, `-B #` | 4, 7 | Bits for each block's scale and offset |
| `-Q` | off | Also write the partition as `quadtree.pgm` |

A lower `-r` gives more, smaller blocks: better quality and a bigger file.

## Decoding

```bash
./decmars                       # lena.ifs -> lena.dec.pgm
./decmars -i -n 8 lena.ifs out.pgm
./decmars -z 2 lena.ifs big.pgm
```

| Option | What it does |
| ------ | ------------ |
| `-n #` | Number of iterations |
| `-i` | Plain iterative decoding at full size, instead of the default pyramidal decoding |
| `-q` | Pyramidal then a few full-size iterations: better quality, advised when zooming |
| `-z #` | Zoom: decode at a different size than the original |
| `-p` | Smooth the block boundaries afterwards |
| `-r` | Write raw instead of PGM |

Zoom factors that aren't powers of two can leave lines on block boundaries
(the one known bug in the [original README](original-README.md)).
