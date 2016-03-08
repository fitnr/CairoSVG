---
layout: page
title: Documentation
permalink: /documentation/
---

## What is CairoSVG?

CairoSVG is a **[SVG 1.1](http://www.w3.org/TR/SVG/) to PNG, PDF, PS** and
SVG converter. It provides both a **command-line interface** and **Python
3.4+** library, for Unix-like operating systems (at least **Linux** and
**OS X**) and **Windows**. It is a free software, distributed under **LGPLv3**.

CairoSVG is written in Python and based on the famous 2D graphics library
called [Cairo](http://cairographics.org/). It is tested on SVG samples coming
from the
[W3C test suite](http://www.w3.org/Graphics/SVG/WG/wiki/Test_Suite_Overview). It
also relies on [lxml](http://lxml.de/) to parse the SVG file, and
[tinycss](http://packages.python.org/tinycss/) plus
[cssselect](http://packages.python.org/cssselect/) to apply CSS. Embedded
raster images are handled by [Pillow](http://python-imaging.github.io/).


## How to use CairoSVG?

### Command line

Here is the simple CairoSVG command line usage:

    $ cairosvg --help
    usage: cairosvg.py [-h] [-v] [-f {pdf,png,ps,svg}] [-d DPI] [-W WIDTH]
                       [-H HEIGHT] [-o OUTPUT]
                       input

    CairoSVG - A simple SVG converter based on Cairo.

    positional arguments:
      input                 input filename or URL

    optional arguments:
      -h, --help            show this help message and exit
      -v, --version         show program's version number and exit
      -f {pdf,png,ps,svg}, --format {pdf,png,ps,svg}
                            output format
      -d DPI, --dpi DPI     ratio between 1 inch and 1 pixel
      -W WIDTH, --width WIDTH
                            width of the parent container in pixels
      -H HEIGHT, --height HEIGHT
                            height of the parent container in pixels
      -o OUTPUT, --output OUTPUT
                            output filename

Supported output formats are `pdf`, `ps`, `png` and `svg` (default is
`pdf`). The default `output` is the standard output. If an output filename is
given, the format is automatically chosen according to the extension.

The `dpi` options sets the ratio between pixels and real-life units such as
millimeters and inches (as explained in the
[specification](http://www.w3.org/TR/SVG11/coords.html)).

The `width` and `height` options can be given to set the container size, for
SVG files whose width and height are using percentages.

Moreover, if `-` is used as filename, CairoSVG reads the SVG string from the
standard input.

### Python

CairoSVG provides a module for Python 3.4+.

The `cairosvg` module offers 4 functions:

- `svg2pdf`,
- `svg2png`,
- `svg2ps`, and
- `svg2svg`.

These functions expect one of these parameters:

- `bytestring`, a byte string containing SVG, or
- `url`, an URL or a filename, or
- `file_obj`, a file-like object.

If the `write_to` argument is provided (filename or file-like object), the
output is written there. Otherwise, the function returns a byte string.


## How good is CairoSVG at following the specification?

CairoSVG is generally good at supporting the widely used features of the SVG
specification, and you'll find here some general information and tips about
that. If you're interested in an exhaustive list of the supported features,
you'll find a full list on [the SVG 1.1 support page](/svg_support/), whose
sections are the same as the ones of the specification.

### Philosophy

CairoSVG is designed to parse well-formed SVG files, and draw them on a
[Cairo](http://cairographics.org/) surface. Cairo is then able to export them
to PDF, PS, PNG, and even SVG files.

The software tries to focus on "real-life features". It's not known to be good
at handling erratic SVG files, with for example unknown syntaxes or unavailable
external resources.

CairoSVG is pretty small, and quite fast once the Python interpreter is
launched. When [Travis](https://travis-ci.org/Kozea/CairoSVG/) launches our
tests, it is able to render more than 270 reference images twice in less than
7.5 seconds (yes, that's about 75 simple images per second).

### Rendering model, document structure, coordinate systems, transformations and units

CairoSVG knows each tag, transformation and unit defined in the specification,
as long as they're not related to interactivity, linking, scripting or
animation. SVG files are parsed with an external XML parser called
[lxml](http://lxml.de).

There should be no real problems with these base features.

### Basic shapes, paths, markers

Basic shapes and paths are very well supported, with no real bug known in
real-life examples. Simple markers are quite well supported, but some of its
advanced features (overflow and relative units for example) are not supported.

### Styling

Styling is possible thanks to both dedicated XML attributes and CSS.

Stylesheets are parsed with an external parser called
[tinycss](http://packages.python.org/tinycss/) and applied using an external
selector-to-XPath converter called
[cssselect](http://packages.python.org/cssselect/): styling is thus pretty
solid. Nevertheless, some minor priority bugs are known (such as `!important`
CSS properties being less important than the ones defined in XML attributes,
for example).

### Colors, gradients, patterns, clipping, masking, filtering, compositing

Colors are generally well supported. The biggest missing features related to
colors are the lack of support for ICC color schemes, missing color
interpolation, and missing gamma correction for external raster images.

Gradients, patterns and clipping are well supported.

Masking is correctly supported, except from odd-even rules, luminance-based
masks and intersections.

Only `feOffset`, `feBlend` and `feFlood` filters are supported. The other
filters are really hard to support, mainly due to the lack of raster filters in
Cairo.

Compositing rules as described in the specification are generally correctly
followed by CairoSVG, meaning that alpha groups are created when needed before
compositing.

### Images

Raster images (including PNG and JPEG) can be included using the
[Pillow](http://python-imaging.github.io/) library. SVG images can be included
using … CairoSVG!

### Text and fonts

Text and features related to fonts are poorly supported.

Simple text based on Latin or Cyrillic alphabets is generally displayed
correctly. Horizontal alignment is quite well supported, really simple vertical
alignment (top, middle, bottom) is supported too.

Other languages, and particularly those based on right-to-left or top-to-bottom
directions are not supported at all.

Nested text (with `tspan` tags) is very well supported.

Individual letter rotation is supported.

Text on path is supported, even with nested, translated `tspan` text.

Fonts are managed by Cairo, which is known to be bad at managing advanced font
features. Font family, size, weight and style are supported for common simple
values, but don't follow the specification on many points (including the font
family choice when multiple values are given). Hinting and anti-aliasing are
supported. Kerning, stretching, font variants, word and letter spacing options,
glyph orientations, baseline shifts and many other properties are not
supported.

Cairo relies on [FreeType](http://freetype.org/) to reach fonts installed on
your system. Many formats are supported, including TTF, OTF and WOFF, but the
fonts need to be installed. As a consequence, it's not possible to reference
fonts thanks to the CSS `@font-face` property.

CairoSVG doesn't support SVG fonts.

### Interactivity, linking, scripting, animation

These features are not applicable or not really interesting in a static
environment, they are not and will not be implemented.