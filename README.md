# PDF Annotation Extractor (Alfred Workflow)

![](https://img.shields.io/github/downloads/chrisgrieser/pdf-annotation-extractor-alfred/total?label=Total%20Downloads&style=plastic) ![](https://img.shields.io/github/v/release/chrisgrieser/pdf-annotation-extractor-alfred?label=Latest%20Release&style=plastic)

An [Alfred Workflow](https://www.alfredapp.com/) to extract Annotations as Markdown & insert Pandoc Citations as References. Outputs Annotations to [Obsidian](https://obsidian.md/), [Drafts](https://getdrafts.com/), PDF, Markdown file, or simply the clipboard.

Automatically determines correct page numbers, merges highlights across page breaks, prepends a YAML Header bibliographic information, and some more small Quality-of-Life conveniences.
<img src="https://user-images.githubusercontent.com/73286100/132963514-f08463cb-de2a-45d2-80fb-8c29afa35fb8.gif" alt="PDF Annotation Extractor" width=60%>

## Table of Contents

<!-- MarkdownTOC -->

- [How to Use](#how-to-use)
	- [Annotation Types extracted](#annotation-types-extracted)
	- [Automatic Page Number Identification](#automatic-page-number-identification)
	- [Annotation Codes](#annotation-codes)
- [Extra Features](#extra-features)
- [Requirements & Installation](#requirements--installation)
- [Configuration](#configuration)
- [Troubleshooting](#troubleshooting)
- [Credits](#credits)
- [About the Developer](#about-the-developer)
	- [Profiles](#profiles)
	- [Donate](#donate)

<!-- /MarkdownTOC -->

## How to Use
- Use the hotkey to trigger the Annotation Extraction of the frontmost document of Preview or PDF Expert. In case Finder is the frontmost app, the currently selected PDF file will be used.

### Annotation Types extracted
- Highlights
- Underlines
- Free Comments
- Strikethroughs

Highlights, Underlines and Strikethroughs are extracted as blockquotes when the have no comments, and as annotated quote when they have a comment. Highlights and Underlines are extracted in visually the same way, while Strikethroughs are extracted as Markdown Strikethroughs.

### Automatic Page Number Identification
The *correct* page numbers will automatically be determined from one of three sources  and inserted into the references as Pandoc Citations, with descending priority:
1. the BibTeX-Library
2. DOI found in the PDF
3. Prompt to manually enter the page number.
	- Enter the __true page number of your first PDF page__. *Example:__ if the first PDF page represents the page number 104, you have to enter `104`.
	- In case there is content before the actual text (e.g. a foreword or a Table of Contents), the first true page often occurs later in the PDF. In that case, you must enter a __negative page number__, reflecting the true page number the first PDF *would have*. *Example:* You PDF is a book which has a foreword, and uses roman numbers for it; true page number 1 is PDF page number 12. If you continued the numbering backwards, the first PDF page would have page number `-10`. So you enter the value `-10` when prompted for a page number.

### Annotation Codes
Insert these special codes at the __beginning__ of an annotation to invoke special actions on that annotation. Annotation Codes do not apply to Strikethroughs. (You can run the Alfred command `acode` to quickly display a cheat sheet showing all the following information.)

- `+`: Merge with previous highlight/underline and puts a "(…)" in between. Used for jumping sections on the same page. If used across pages, both pages will be included in the citation, and the "(…)" will be omitted, assuming the continuation of a of across page borders.
- `? foo` __(free comments)__: Turns "foo" into a __"Pseudo-Admonitions"__ [^1] (`>>>`) and move up. (Used for Introductory Comments or Questions).
- `##`: Turns highlighted/underlined text into a __heading__ that is added at that location. The number of `#` determines the heading level. If the annotation is a free comment, the text following the `#` is used as heading instead (Space after `#` required). Free comments can be sued for manual headings not appearing in text.
- `---` __(free comments)__: Inserts a markdown __hr__ (`---`) and removes the annotation.
- `X` Turns highlighted/underlines text into a __task__ and move up. If the annotation is a free comment, the text following the `X` will be used as task text.
- `=`: Adds highlighted/underlined text as __tags__ to the YAML-frontmatter (mostly used for Obsidian as output). If the annotation is a free comment, uses the text after the `=`. In both cases, the annotation is removed afterwards.
- __Images__ (Obsidian only): Take a screenshot with the set hotkey you set. The image will be saved to the folder `attachments` in the Obsidian destination folder and renamed with the citekey. You can then use the annotation code `!n foo` __(free comments)__ to insert the n-th image taken with the image-hotkey at the location of the comment location. "n" is the number of images taken, e.g. "!3" for the third image. "foo" will be added as image alt-text (image label).
- `_` __(highlights only)__: Removes the `_` and creates a copy of the annotation, but with the type `underline`. Intended for use when the split-off of underlines is enabled, and will do nothing if it is disabled. This annotation code avoids having to highlight *and* underline the same text segment to have it in both places.

## Extra Features
- When using Obsidian, the wikilink is also copied to the clipboard after annotation extraction.
- With the output type set to Obsidian or Markdown, a YAML-Header with bibliographic information (author, title, citekey, year, keywords, etc.) is also prepended.

## Requirements & Installation

1. Requirements
	- Alfred (Mac only)
	- [Alfred Powerpack](https://www.alfredapp.com/shop/) (~30€)
	- References saved as BibTeX-Library (`.bib`)
2. Install Dependencies
	- Don't be discouraged if you are not familiar with the Terminal. Just copy-paste the following code into your Terminal and press enter – there is nothing more you have to do. (It may take a moment to download and install everything.)

	```bash
	brew install python3 # newer version of pip3 needed
	pip3 install pdfannots
	```

3. Download
	- Download and install the [PDF Annotation Extractor Workflow](https://github.com/chrisgrieser/pdf-annotation-extractor-alfred/releases/latest/)

4. Set the Hotkey
	- Set the Hotkey by double-clicking this field:
	<img width=18% alt="Set Hotkey" src="https://user-images.githubusercontent.com/73286100/132960488-a60eff61-16a9-42cf-801f-c42612fbfb5e.png">

5. Set BibTeX Library Path
	- using the `aconf` command, select `Set BibTeX Library`, and then search/select your `.bib` file

6. Optional: only required for specific output types
	- *Obsidian as Output*: Use the `aconf` command, select `Obsidian Destination`, and then search/select the folder.
	- *PDF as Output Format*: Install Pandoc and a [PDF-Engine](https://pandoc.org/MANUAL.html#option--pdf-engine) of your choice.

	```bash
	# with Homebrew
	brew install pandoc
	brew install wkhtmltopdf # can be changed to a pdf-engine of your choice
	```

## Configuration
*Use the Alfred keyword `aconf` for the configuration of this workflow.*

- The output format (PDF, Markdown, Clipboard, [Drafts](https://getdrafts.com/), or [Obsidian](https://obsidian.md/)). When selecting Markdown or Obsidian as output format, a YAML-Header with information from your BibTeX Library will be prepended.
- Set whether citekeys should be entered manually or determined automatically via filename. The latter requires a filename beginning with the citekey, followed by an underscore: `{citekey}_{...}.pdf`. You can easily achieve such a filename pattern with via renaming rules of most reference managers, for example with the [ZotFile plugin for Zotero](http://zotfile.com/#renaming-rules) or the [AutoFile feature of BibDesk](https://bibdesk.sourceforge.io/manual/BibDeskHelp_77.html#SEC140).
- In case you are the PDF is not part of your BibTeX Library (e.g., a manuscript from colleague), you can also choose to deactivate the usage of BibTeX metadata and citekeys.
- The Obsidian destination must be a folder in your vault.
- Select whether any annotations of the type `underlines` should be split off and moved to a second output instead (currently only Drafts is supported).

## Troubleshooting
- Upgrade to the newest version of pdfannots: `pip3 install --upgrade pdfannots`
- This workflow won't work with annotations that are not actually saved in the PDF file. Some PDF Readers like __Skim__ do this, but you can [tell those PDF readers to save the notes in the actual PDF.](https://skim-app.sourceforge.io/manual/SkimHelp_45.html)
- The workflow sometimes does not work when the pdf contains bigger free-form annotations (e.g. from using a stylus on a tablet). Delete all annotations that are "image" or "free form" and the workflow should work.
- When the hotkey does not work when triggered in Preview or PDF Expert, most likely the Alfred app does not have permission to access them. You can give Alfred permission in the Mac OS System Settings:
<img src="https://i.imgur.com/ylGDs2f.png" alt="Permission for Alfred to access Preview" width=30%>

ℹ️ When you cannot resolve the problem, please [open an GitHub issue](https://github.com/chrisgrieser/pdf-annotation-extractor-alfred/issues).

## Credits
Thanks to [Andrew Baumann for pdfannots](https://github.com/0xabu/pdfannots) without which this Alfred Workflow would not be possible. Also thanks to @StPag from the Obsidian Discord Server for his ideas on annotation codes.

## About the Developer
In my day job, I am a sociologist studying the social mechanisms underlying the digital economy. For my PhD project, I investigate the governance of the app economy and how software ecosystems manage the tension between innovation and compatibility. If you are interested in this subject, feel free to get in touch!

### Profiles
- [Academic Website](https://chris-grieser.de/)
- [Discord](https://discordapp.com/users/462774483044794368/)
- [GitHub](https://github.com/chrisgrieser/)
- [Twitter](https://twitter.com/pseudo_meta)
- [ResearchGate](https://www.researchgate.net/profile/Christopher-Grieser)
- [LinkedIn](https://www.linkedin.com/in/christopher-grieser-ba693b17a/) <!-- markdown-link-check-disable-line -->

### Donate
- [PayPal](https://www.paypal.com/paypalme/ChrisGrieser)
- [Ko-Fi](https://ko-fi.com/pseudometa) <!-- markdown-link-check-disable-line -->

[⬆️ Go Back to Top](#Table-of-Contents)

[^1]: These are exclusive to Obsidian with [certain themes](https://github.com/chrisgrieser/shimmering-focus).
