# OP2ArtUtils
Python utilities to extract and rebuild Outpost 2 art files (prt and bmp) to/from their component bitmaps, palettes, and metadata for inspection/editing.

## Installation
Create a venv and install PyYAML:
```
$ python -m venv venv
$ . venv/bin/activate  # or venv\\bin\\Activate.ps1 on Windows
$ pip install -r requirements.txt
```

## Usage

### Dumping

Make sure you have copies of op2\_art.prt and OP2\_ART.BMP.  If using the OPU unofficial patch, OP2\_ART.BMP should be in your base Outpost2 dir and op2\_art.prt will have been extracted to OPU\\base\\sprites in your OP2 dir.

Run art\_extractor.py:

```
$ python art_extractor.py <path to op2_art.prt> <path to op2_art.bmp> <output dir>
```

This will produce the following outputs in the directory specified:
- `palettes/*.pal` - palette files in (default) Microsoft RIFF PAL format (most graphics editing software should be able to handle these, but you can also specify `--palette_format=pal|text|act` to generate PSP-style text .pal files or Photoshop .act files). The name of the file is the 0-indexed ID of the palette from the PRT.
- `bitmaps/*.bmp` - bitmap files in Microsoft BMP format. Again, the name of the file is the bitmap index. Most bitmaps are 8bpp (a handful of image types are 1bpp). The color table is embedded in the bitmap file for display, but of course this will be ignored by the code that repacks the PRT (when we get around to that).
- `bitmaps.yml` - metadata information about the bitmaps in YAML format (currently just a mapping of ID to palette index and type) as well as the number of palettes.
- `animations.yml` - animation metadata. This makes up the vast majority of information in the PRT file, containing information such as bounding boxes, frame and subframe information, "unknown" fields such as "optional" data and "appendices", etc.
- `extra.dat` - extra binary data appended to the end of the PRT that the game doesn't read (purpose unknown).

At this point you can edit any of the extracted files.

### Rebuilding

Run art\_builder.py:

```
$ python art_builder.py <input dir> <path to op2_art.prt> <path to op2_art.bmp>
```

This will repack the metadata, bitmaps, and palettes in the input directory back into the PRT/BMP files.  Note that the output files will be overwritten.  As with extraction, you can specify `--palette_format` to override the palette file format.  You can also specify `--palettes_from_bitmaps` to ignore the palette data and generate a palette for each bitmap (untested, and will increase file size and load time, but allows for greater flexibility with colors).

## Known Issues/Caveats

- Very little validation is done on the input data when building the PRT/BMP. If you use invalid metadata, it will silently produce an invalid PRT file.
- If the PRT file is invalid, Outpost2.exe will simply fail to start with no warning or error message whatsoever.
- The output .BMP file is larger than the original one (this is likely due to incorrect alignment as well as the fact that too much data is read/stored for 1bpp graphics), though it seems to work fine in game.
- The output .PRT is smaller than the original one. This likely means there's additional data at the end of the file. In any case, the game doesn't read this data and we don't know what it's used for (yet).
- The animation metadata file also contains a `num_optional_entries` entry, since we didn't know where else to put this and it's not clear what this number actually represents. (it is not the number of "optional" data blocks or appendices), so we have to store it for now and reuse it when importing the data.
- Usual disclaimer, if this program crashes your computer, corrupts your OP2 installation, etc., we're not responsible. All use is at your own risk.

## Copyright

In short: you're free to do what you want with this code, just ensure this copyright notice is included with it. Thanks!

The MIT License (MIT)

Copyright (c) 2015

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.
