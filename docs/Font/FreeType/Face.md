[[Raku PDF Project]](https://pdf-raku.github.io)
 / [[Font-FreeType Module]](https://pdf-raku.github.io/Font-FreeType-raku)
 / [Font::FreeType](https://pdf-raku.github.io/Font-FreeType-raku/Font/FreeType)
 :: [Face](https://pdf-raku.github.io/Font-FreeType-raku/Font/FreeType/Face)

class Font::FreeType::Face
--------------------------

Font typefaces loaded from Font::FreeType

Synopsis
--------

    use Font::FreeType;
    use Font::FreeType::Face;

    my Font::FreeType $freetype .= new;
    my Font::Freetype::face $vera = $freetype.face('Vera.ttf');

Description
-----------

This class represents a font face (or typeface) loaded from a font file. Usually a face represents all the information in the font file (such as a TTF file), although it is possible to have multiple faces in a single file.

Never 'use' this module directly; the class is loaded automatically from Font::FreeType. Use the `Font::FreeType.face()` method to create a new Font::FreeType::Face object from a filename and then use the `iterate-chars()`, or `iterate-glyphs()` methods.

Methods
-------

Unless otherwise stated, all methods will die if there is an error.

### ascender()

The height above the baseline of the 'top' of the font's glyphs, scaled to the current size of the face.

### attach-file(_filename_) *** NYI ***

Informs FreeType of an ancillary file needed for reading the font. Hasn't been tested yet.

### font-format()

Return a string describing the format of a given face. Possible values are ‘TrueType’, ‘Type 1’, ‘BDF’, ‘PCF’, ‘Type 42’, ‘CID Type 1’, ‘CFF’, ‘PFR’, and ‘Windows FNT’.

### face-index()

The index number of the current font face. Usually this will be zero, which is the default. See `Font::FreeType.face()` for how to load other faces from the same file.

### descender()

The depth below the baseline of the 'bottom' of the font's glyphs, scaled to the current size of the face. Actually represents the distance moving up from the baseline, so usually negative.

### family-name()

A string containing the name of the family this font claims to be from.

### fixed-sizes()

Returns an array of Font::FreeType::BitMap::Size objects which detail sizes. Each object has the following available methods:

  * *size*

    Size of the glyphs in points. Only available with Freetype 2.1.5 or newer.

  * *height*

    Height of the bitmaps in pixels.

  * *width*

    Width of the bitmaps in pixels.

  * *x-res(:dpi)*, *y-res(:dpi)*

    Resolution the bitmaps were designed for, in dots per inch. Only available with Freetype 2.1.5 or newer.

  * *x-res(:ppem)*, *y-res(:ppem)*

    Resolution the bitmaps were designed for, in pixels per em. Only available with Freetype 2.1.5 or newer.

### glyph-images(str)

Returns an array of [Font::FreeType::GlyphImage](https://pdf-raku.github.io/Font-FreeType-raku/Font/FreeType/GlyphImage) objects for the Unicode string.

For example, to load particular glyphs (character images):

    for $face.glyph-images('ABC') {
        # Glyphs can be rendered to bitmap images, among other things:
        my $bitmap = .bitmap;
        say $bitmap.Str;
    }

### iterate-chars($text?)

    for $face.iterate-chars -> Font::FreeType::Glyph $glyph { ... }

Iterates through all the characters in the text, and returns the corresponding [Font::FreeType::Glyph](https://pdf-raku.github.io/Font-FreeType-raku/Font/FreeType/Glyph) object for each of them in turn. Glyphs which don't correspond to Unicode characters are ignored.

Each time your callback code is called, a object is passed for the current glyph.

If there was an error loading the glyph, then the glyph's, `stat` method will return non-zero and the `error` method will return an exception object.

If `$text` is ommitted, all Unicode mapped characters in the font are iterated.

### iterate-glyphs()

    for $face.iterate-glyphs -> Font::FreeType::Glyph $glyph { ... }

Iterates through all the glyphs in the font, and returns [Font::FreeType::Glyph](https://pdf-raku.github.io/Font-FreeType-raku/Font/FreeType/Glyph) objects.

If there was an error loading the glyph, then the glyph's, `stat` method will return non-zero and the `error` method will return an exception object.

### has-glyph-names()

True if individual glyphs have names. If so, the names can be retrieved with the `name()` method on [Font::FreeType::Glyph](https://pdf-raku.github.io/Font-FreeType-raku/Font/FreeType/Glyph) objects.

See also `has-reliable-glyph-names()` below.

### has-horizontal-metrics()

### has-vertical-metrics()

These return true if the font contains metrics for the corresponding directional layout. Most fonts will contain horizontal metrics, describing (for example) how the characters should be spaced out across a page when being written horizontally like English. Some fonts, such as Chinese ones, may contain vertical metrics as well, allowing typesetting down the page.

### has-kerning()

True if the font provides kerning information. See the `kerning()` method below.

### has-reliable-glyph-names()

True if the font contains reliable PostScript glyph names. Some Some fonts contain bad glyph names.

See also `has-glyph-names()` above.

### height()

The line height of the text, i.e. distance between baselines of two lines of text.

### is-bold()

True if the font claims to be in a bold style.

### is-fixed-width()

True if all the characters in the font are the same width. Will be true for mono-spaced fonts like Courier.

### is-italic()

Returns true if the font claims to be in an italic style.

### is-scalable()

True if the font has a scalable outline, meaning it can be rendered nicely at virtually any size. Returns false for bitmap fonts.

### is-sfnt()

True if the font file is in the 'sfnt' format, meaning it is either TrueType or OpenType. This isn't much use yet, but future versions of this library might provide access to extra information about sfnt fonts.

### kerning(_left-char_, _right-char_, :mode)

Returns a vector for the the suggested kerning adjustment between two glyphs.

For example:

    my $kern = $face.kerning('A', 'V');
    my $kern-distance = $kern.x;

The `mode` option controls how the kerning is calculated, with the following options available:

  * *FT_KERNING_DEFAULT*

    Grid-fitting (hinting) and scaling are done. Use this when rendering glyphs to bitmaps to make the kerning take the resolution of the output in to account.

  * *FT_KERNING_UNFITTED*

    Scaling is done, but not hinting. Use this when extracting the outlines of glyphs. If you used the `FT_LOAD_NO_HINTING` option when creating the face then use this when calculating the kerning.

  * *FT_KERNING_UNSCALED*

    Leave the measurements in font units, without scaling, and without hinting.

### num-faces()

The number of faces contained in the file from which this one was created. Usually there is only one. See `Font::FreeType.face()` for how to load the others if there are more.

### num-glyphs()

The number of glyphs in the font face.

### postscript-name()

A string containing the PostScript name of the font, or _undef_ if it doesn't have one.

### glyph-name(char)

Returns the name for the given character, where `char` can be a string or code-point number

### glyph-index(char)

Returns the glyph index in the font, or 0 if the character does not exist. `char` can be a string or code-point number

### glyph-name-from-index(index)

Returns the name for the given character.

### glyph-index-from-glyph-name(index)

Returns the glyph index for the given glyph name.

### set-char-size(_width_, _height_, _x-res_, _y-res_)

Set the size at which glyphs should be rendered. Metrics are also scaled to match. The width and height will usually be the same, and are in points. The resolution is in dots-per-inch.

When generating PostScript outlines a resolution of 72 will scale to PostScript points.

### set-pixel-sizes(_width_, _height_)

Set the size at which bit-mapped fonts will be loaded. Bitmap fonts are automatically set to the first available standard size, so this usually isn't needed.

### style-name()

A string describing the style of the font, such as 'Roman' or 'Demi Bold'. Most TrueType fonts are just 'Regular'.

### underline-position()

### underline-thickness()

The suggested position and thickness of underlining for the font, or _undef_ if the information isn't provided. Currently in font units, but this is likely to be changed in a future version.

### units-per-EM()

The size of the em square used by the font designer. This can be used to scale font-specific measurements to the right size, although that's usually done for you by FreeType. Usually this is 2048 for TrueType fonts.

### charmap()

The current active [Font::FreeType::CharMap](https://pdf-raku.github.io/Font-FreeType-raku/Font/FreeType/CharMap) object for this face.

### charmaps()

An array of the available [Font::FreeType::CharMap](https://pdf-raku.github.io/Font-FreeType-raku/Font/FreeType/CharMap) objects for the face.

### bounding-box()

The outline's bounding box for this face.

### raw()

    use Font::FreeType::Raw;
    use Cairo;
    my FT_Face $ft-face-raw = $face.raw;
    $ft-face-raw.FT_Reference_Face;
    my Cairo::Font $font .= create(
         $ft-face-raw, :free-type,
    );
    # some time later...
    $ft-face.FT_Done_Face;
    $ft-face = Nil;

This method provides access to the underlying raw FT_Face native struct; for example, for integration with the [Cairo](Cairo) graphics library.

The `FT_Reference_Face` and `FT_Done_Face` methods will need to be called if the struct outlives the parent `$face` object.

### protect()

This method should only be needed if the low level native freetype bindings are being use directly. See [Font::FreeType::Raw](https://pdf-raku.github.io/Font-FreeType-raku/Font/FreeType/Raw). 

See Also
--------

  * [Font::FreeType](https://pdf-raku.github.io/Font-FreeType-raku/Font/FreeType)

  * [Font::FreeType::Glyph](https://pdf-raku.github.io/Font-FreeType-raku/Font/FreeType/Glyph)

  * [Font::FreeType::GlyphImage](https://pdf-raku.github.io/Font-FreeType-raku/Font/FreeType/GlyphImage)

Author
------

Geoff Richards <qef@laxan.com>

Ivan Baidakou <dmol@cpan.org>

David Warring <david.warring@gmail.com> (Raku Port)

COPYRIGHT
=========

Copyright 2004, Geoff Richards.

Ported from Perl to Raku by David Warring Copyright 2017.

This library is free software; you can redistribute it and/or modify it under the same terms as Perl itself.

