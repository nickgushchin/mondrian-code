# Mondrian Code

A writing system in Piet Mondrian's five colours. Write any text in ten scripts (v7): Latin script (English, German, Swiss German, French, Italian, Spanish, Portuguese, Dutch, Danish, Norwegian, Swedish, Finnish…), Cyrillic (Russian, Ukrainian, Belarusian, Bulgarian, Serbian, Macedonian), Chinese, Korean, Japanese kana, Arabic, Hebrew, Greek, Hindi (Devanagari) or Ge'ez (Amharic). African Latin-script languages (Swahili, Yoruba, Hausa, Igbo, Wolof, Lingala…) are covered too. The encoder shows every text as five outputs at once (v7): a square mosaic, a Mondrian composition (square, landscape or portrait; Composition, Mondrian blocks or a plain grid), a mosaic inside a famous painting, one line, and the code string. Every output reads back.

**Try it:** https://nickgushchin.github.io/mondrian-code/

**Watch it:** a 2-minute explainer video plays at the top of the page.

## Rules

1. **Three colours are the digits.** Red = 0, yellow = 1, blue = 2. One cell holds one trit (a ternary digit, about 1.58 bits).
2. **Short codes for frequent letters.** A character is 1 to 4 colour cells. Latin is ordered by English letter frequency, Cyrillic by Russian: E, T, A (О, Е, А) take one cell, the next nine take two, the rest take three. Digits take three; punctuation and accented or national letters (Ä Ö Ü ß É Ç Ñ Å Ø …, Є І Ї Ґ Ў Ђ Ј Љ Њ Ћ Џ …) take four.
3. **White and black close every character.** White = end of a letter, black = end of a word (no space cell needed).
4. **Empty cells carry no meaning.** A marker that follows another marker is a filler and is skipped, so any text fills its frame exactly: the smallest square (side = √cells, rounded up), landscape (about 3:2) or portrait (about 2:3) grid that holds the text and the key, with the spare white cells spread evenly between characters.
5. **Four reading directions:** left to right, right to left (rows), top to bottom, bottom to top (columns), in every format.
6. **Direction key:** 8 colour cells in a row: R Y B R Y fixed + 3 script cells (27 slots; Latin BRR, Cyrillic BRY, Chinese BRB, Korean RRR, Japanese RRY, Arabic RRB, Hebrew RYR, Greek RYY, Hindi RYB, Ge'ez RBR). v3 paintings read unchanged. No character is longer than 7 cells, so the run never occurs in text. It starts at cell F(n) mod n² (Fibonacci, n = grid side) in reading order; a W × H rectangle uses F(longer side) mod W·H, the same rule for squares, so square paintings are unchanged from v4; the reader tries all four directions and keeps the one with the key in place, which also gives the script.
7. **Escape:** any other Unicode character is written as an escape code plus its code point in two 6-cell parts.
8. **Mondrian blocks (v5):** a rendering style, not a new code. Touching cells of the same colour are drawn as one rectangle without the black lines between them (the run-length encoding idea). Each same-colour area is split into rectangles, largest first, so an L-shape becomes two blocks. The reader still samples every cell centre, so block paintings read back like grid paintings.
9. **Composition (v6, the default style):** about 60% white (70% with *White space: More*) in 2–4 reserved white rectangles, crossed by 2–5 black lines of varied thickness that run edge to edge or up to another line, no thick frame. The rectangles are reserved before the text is laid out; the text flows around them in reading order and a letter is never split (white fillers are added at a character boundary until the next letter fits). **The white rectangles are fillers** (each reserved cell follows a marker, rule 4), so the reader needed no change. **The lines are decoration on the lattice:** they lie on cell borders, are at most about a third of a cell thick, never touch a cell centre, and are chosen from the code itself, so the same text always paints the same. Short texts look most like Mondrian; long texts stop growing at 40 cells a side and stay colour-dense. The lines carry no data here; a "true" Mondrian where the lines themselves carry the text is a separate research track.

10. **Painting fills (v7, output 3):** the same cells on a square lattice clipped to one part of a public-domain painting (figure or background), stored on the page as polygons in 0–1 image coordinates. Lattice cells whose centre lies inside the region, in reading order (four directions), carry the 8-cell key first, then the text; spare cells are white fillers drawn as a light veil over the painting (70% white, which never reads as red, yellow or blue). The cell size is the largest that holds the message (and leaves at least 150 cells, so the shape still reads). This is an art output: it reads back only on this page, which knows the painting, the polygon and the lattice; the PNG file name (`mondrian-code-<painting>-<region>-s<cell>.png`) carries the painting and cell size as a fallback, and without it the reader searches every painting of that image shape, both regions and every cell size.

**Chinese:** the 3,249 most frequent characters (Jun Da, Modern Chinese corpus; 99.4% of text) in 1 to 7 cells.

## Where the ideas come from

| Rule | Same idea in |
|---|---|
| Frequent letters get the shortest codes | **Huffman coding** (1952), inside ZIP, gzip, PNG (DEFLATE), JPEG and MP3 |
| E is one cell, Q is three | **Morse code** (1830s–40s) |
| Frequent pieces become short codes | **LLM tokenizers** (byte-pair encoding) |
| Variable-length characters kept in sync | **UTF-8** (1992) lead/continuation bits |
| White/black closes each character | **Start/stop bits** on serial lines; Morse pauses |
| Empty cells are skipped | **Padding** in QR codes and file formats |
| Same-colour neighbours merge into one block | **Run-length encoding** (1967), fax Group 3 (1980), TIFF PackBits, JPEG zero runs |
| Three colours = trits | **Setun** ternary computer (1958) |

English letters average 1.89 colour cells (vs 3 fixed-length); with the marker 2.89 cells. Shannon's limit for a separator-free ternary code is about 2.63. The marker is the deliberate trade-off against a true prefix-free Huffman code: readability and recovery after a misread cell.

## Compared with the bit system (per English letter)

| Code | States/cell | Cells/letter | Efficiency |
|---|---|---|---|
| ASCII / UTF-8 | 2 | 8 | 52% |
| Fixed 5-bit | 2 | 5 | 83% |
| Binary Huffman (ZIP-style) | 2 | 4.20 | 99% |
| Fixed ternary (3 trits) | 3 | 3 | 88% |
| Ternary Huffman, no separators | 3 | 2.70 | 97% |
| **Mondrian Code** | 5 | **2.89** | 62% |

Fewest squares of any eye-readable code (31% fewer than binary Huffman); the white/black markers cost information efficiency but make letters visible and recoverable. Spaces are free (inside the black marker).

The same colours mean different letters in each script; the direction key names the script.

**Every character's colours, script by script** (all ten scripts, including the 3,249 Chinese characters): on the page under *How it works → The alphabet*. The tables are generated from the encoder itself, so they always match it.

## Files

- `index.html`: the whole thing (description, encoder with five outputs, decoder and image reader) in one static page, no dependencies.
- `paintings/`: the six paintings for the painting fills (JPEG, longest side at most 1,200 px) and their thumbnails. See the image credits below.
- `video/`: the 2-minute explainer (MP4) and its poster. AI voice narration. Music: "Heroic Age" by Kevin MacLeod (incompetech.com), licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). The MIT licence covers the code, not the music.

## Image credits (painting fills)

All six are public-domain works (the artists died more than 70 years ago) shown in the exhibition [“Ikonen. Ausgewählte Werke der Sammlung”](https://www.kunsthaus.ch/besuch-planen/ausstellungen/ikonen/) at Kunsthaus Zürich. The image files come from Wikimedia Commons, where they are marked public domain (faithful reproductions of 2D public-domain art, PD-Art / PD-old). No photographs from kunsthaus.ch are used. The files were cropped to the painting and resized; the MIT licence covers the code, not these images.

| Painting | Artist (died) | Year | Collection | Source and licence |
|---|---|---|---|---|
| Baum am Brienzersee vom Bödeli aus | Ferdinand Hodler (1918) | 1906 | Kunsthaus Zürich, gift 2023 | [Commons](https://commons.wikimedia.org/wiki/File:Hodler_-_Baum_am_Brienzersee_vom_B%C3%B6deli_aus_-_1906.jpg), public domain (PD-old) |
| Composition with Red, Blue and Yellow | Piet Mondrian (1944) | 1930 | Kunsthaus Zürich, gift of Alfred Roth 1987 | [Commons](https://commons.wikimedia.org/wiki/File:Piet_Mondriaan,_1930_-_Mondrian_Composition_II_in_Red,_Blue,_and_Yellow.jpg), public domain (PD-Art) |
| Titania liebkost Zettel mit dem Eselskopf | Johann Heinrich Füssli (1825) | 1793–94 | Kunsthaus Zürich, 1941 | [Commons](https://commons.wikimedia.org/wiki/File:Johann_Heinrich_F%C3%BCssli_-_Titania_liebkost_den_eselk%C3%B6pfigen_Bottom.jpg), public domain (PD-Art) |
| Der heilige Antonius predigt den Fischen (upper panel) | Arnold Böcklin (1901) | 1892 | on view at Kunsthaus Zürich | [Commons](https://commons.wikimedia.org/wiki/File:B%C3%B6cklin_Der_Heilige_Antonius_1892.jpg), public domain (PD-Art) |
| Signac et ses amis en barque | Pierre Bonnard (1947) | about 1924 | Kunsthaus Zürich, 1932 | [Commons](https://commons.wikimedia.org/wiki/File:Signac_et_ses_amis_en_bateau.jpg), public domain (PD-Art) |
| La Maison du Père Pilon | Vincent van Gogh (1890) | 1890 | private collection, on loan to the exhibition | [Commons](https://commons.wikimedia.org/wiki/File:Van_Gogh_-_Das_Haus_von_P%C3%A8re_Pilon.jpeg), public domain (PD-old) |

The region polygons were drafted with an image segmentation model (SAM) on a computer and then checked and corrected by eye; the page itself uses no AI, only the stored polygons.

Not encryption: anyone can break it by counting colours.

Designed by [Nick Gushchin](https://nickgushchin.github.io), October 2026. MIT licence.
