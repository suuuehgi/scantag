# scantag - Scan to Tagged OCRed PDFs

This is a wrapper script around [`scanimage`][scanimage], [`noteshrunk`][noteshrunk], [`ocrmypdf`][ocrmypdf] and [`ftag`][TMSU].

It is designed for use with an Automatic Document Feeder (ADF).
It scans a whole stack of pages with `scanimage`, optimizes, shrinks and converts them to PDF with `noteshrunk`, does a character recognition with `ocrmypdf` and tags the resulting file with `tsmu`.

The intermediate images and pdfs are all stored in a temporary folder in the current working directory (so you can choose between disk and RAM [e.g. by running from `/tmp`]).


## Requirements

- [scanimage][scanimage] (`sane-backends`)
- [noteshrunk][noteshrunk]
- [OCRmyPDF][ocrmypdf]
- [ftag][TMSU]

## Configuration

Set `SCANNER` and `OCR_LANG` in the config section of the script.

```
#################### CONFIG ####################

# See `scanimage --list-devices`
SCANNER = 'fujitsu:fi-6110dj:1206658'

################################################
```

### Auto-Completion

For [argument completion](https://pypi.org/project/argcomplete/) use (BASH)
```bash
eval "$(register-python-argcomplete scan)"
```

or e.g.

```fish
register-python-argcomplete --shell fish scan | source
```

for other shells (see [here](https://github.com/kislyuk/argcomplete/tree/develop/contrib)).

## Usage

```
usage: scantag [-h] [-m {single,duplex}] [-c {lineart,gray,color}] [-p {text,ctext,smusic}] [-r {50..600}] [-t TAGS] [-td TDATE] [-s {a4,a5}] [--text]
               [--scan] [-k] [--nsparams NSPARAMS] [--skip-ocr] [-l LANGUAGE] [-d] [-v] [-y] [--version]
               [output_file]

Scan a document and save it as an optimized, OCRed and tagged PDF.

positional arguments:
  output_file           Output File Name for the Scanned Document (default: output.pdf)

options:
  -h, --help            show this help message and exit
  -m, --mode {single,duplex}
                        Single sided / duplex scanning. The side facing away from the user is the <single> page / the first page of the <duplex> mode.
                        (default: duplex)
  -c, --color {lineart,gray,color}
                        Color Mode: Lineart / Gray / Color (default: color)
  -p, --preset {text,ctext,smusic}
                        Presets:"text": A preset for text that results in black-and-white scans - corresponds to '--color gray --resolution 400 --nsparams
                        "--skip_empty --white_background --binarize --normalize --denoise_opening --opening_strength 1 --unsharp_mask --unsharp_amount 5
                        --unsharp_radius 15"',"ctext": A preset for text scans including colors - corresponds to '--color color --resolution 400
                        --nsparams "--skip_empty --white_background --black --normalize --n_colors 10 --denoise_opening --opening_strength 1
                        --unsharp_mask --unsharp_amount 1 --unsharp_radius 1"',"smusic": A preset for sheet music scans - corresponds to '--color gray
                        --resolution 400 --nsparams "--skip_empty --white_background --binarize --normalize --denoise_opening --opening_strength 1.9
                        --unsharp_mask --unsharp_amount 5 --unsharp_radius 15"' (default: )
  -r, --resolution {50..600}
                        Scanning Resolution (default: 300)
  -t, --tags TAGS       Comma-separated list of tags to be used with ftag. E.g. "tag1, tag2, tag3" (default: None)
  -td, --tdate TDATE    Add date tag in the form YYYY-MM-DD. (default: None)
  -s, --paper-size {a4,a5}
                        Paper Size for Scanning (default: a4)
  --text                Short for --preset="text" - A preset for text that results in black-and-white scans - corresponds to '--color gray --resolution
                        400 --nsparams "--skip_empty --white_background --binarize --normalize --denoise_opening --opening_strength 1 --unsharp_mask
                        --unsharp_amount 5 --unsharp_radius 15"' (default: False)
  --scan                Just scan to images, skip the rest. (default: False)
  -k, --keep_intermediate
                        Do not delete intermediate scans afterwards. (default: False)
  --nsparams NSPARAMS   Arguments to be used for noteshrunk (see noteshrunk -h). (default: -w -s --unsharp_mask)
  --skip-ocr            Skip OCR on the scanned document. (default: False)
  -l, --language LANGUAGE
                        Tesseract OCR language: eng, deu, spa, fra, ita, ... Can be used multiple times for multiple languages. See `tesseract --list-
                        langs` for installed languages or here for further information: https://ocrmypdf.readthedocs.io/en/latest/languages.html (default:
                        ['eng', 'deu'])
  -d, --deskew          Pass the --deskew flag to ocrmypdf for automatic deskewing. (default: False)
  -v, --verbose         Increase verbosity. Use twice to activate flushing of subprocess stdout to terminal. (default: 0)
  -y, --overwrite       Answer all questions with Yes. Overwrite existing files without asking. (default: False)
  --version             Show program version and exit
```

## Example

```fish
> scantag --text -m single -t 'invoice, car, accident' --tdate 2026-05-01 --deskew letter.pdf
> ftag list letter.pdf
Tags : invoice, car, accident
Date : 2026-05-01
> tagsearch '#invoice and date > 2020'
./letter.pdf
```
(For `tagsearch` see [here][tagsearch].)

[scanimage]: http://www.sane-project.org/man/scanimage.1.html
[noteshrunk]: https://github.com/suuuehgi/noteshrunk
[ocrmypdf]: https://github.com/ocrmypdf/OCRmyPDF
[ftag]: https://github.com/suuuehgi/scripts/blob/master/ftag.py
[tagsearch]: https://github.com/suuuehgi/scripts/blob/master/tagsearch.py
