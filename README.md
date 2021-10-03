# Simple guide to write academic papers in markdown

## Preface

I hate to write in latex, 
having to worry about formatting while writing academic papers takes focus out of the content.

So, I am trying to put together a guide to make it possible to use markdown for writing papers.

## Objectives

The main goal of this endeavour is to make it possible to use latex only for formatting the document, 
everything else should be in markdown.

## Disclaimer

Some institutions may have requirements that are not covered by this solution, 
I made this for myself and made it public so maybe others with the same anguish as me can get some help as well.
Proceed at your own risk.

I am not responsible for infringements of academic rules.

This was only reproduced on Arch linux.
I tested Debian 11 as well but my acronyms filter doesn't work. Using another or none at all might work for you. 

# The software

This guide will use [pandoc](https://pandoc.org/), it's [markdown](https://garrettgman.github.io/rmarkdown/authoring_pandoc_markdown.html) flavour
and some other optional filters.

In the end, the user will only have to manage 2-3 files:

- a **latex** template file, for the document template
- a **markdown** file, with all the content for the paper
- an optional **yml** file, for the document's metadata. (*can be embeded in the markdown file or not needed at all*)

## Optional filters

Pandoc's markdown does not support some features that are essential for academic writing. 
The following table details the filters I found to work best for me.

filter|feature
------|------
[pandoc-crossref](https://github.com/lierdakil/pandoc-crossref)|numbers and references figures, equations and tables
pandacro|simple acronym functionality using [acro](https://ctan.org/pkg/acro)

These filters are optional and swappable, these are the ones that worked for me.

# Installation

## Arch Linux

Install the dependencies
```shell
sudo pacman -S pandoc texlive-core python3-pip wget unzip
```

If you wish the [pandoc-crossref](https://github.com/lierdakil/pandoc-crossref) filter, run
```shell
sudo pacman -S pandoc-crossref
```

If you wish to use the [acro](https://ctan.org/pkg/acro) latex package, run
```shell
# assuming these are run in the path where the document is
# these instructions can be changed, 
# you just need to put the package in a path searched by latex
wget http://mirrors.ctan.org/macros/latex/contrib/translations/translations.sty
```

If you wish to use the pandacro filter, run
```shell
pip install --user panflute

# assuming these are run in the path where the document is
# these instructions can be changed, 
# you just need to put the package in a path searched by latex
wget https://mirrors.ctan.org/macros/latex/contrib/acro.zip
unzip acro.zip
cp acro/acro.sty .
```

# Usage

All external programs have documentation linked in this guide, so only brief illustrative examples will be provided here.
The filters made by myself will have their full documentation in this section.

**NOTE**: The [pandoc user guide](https://garrettgman.github.io/rmarkdown/authoring_pandoc_markdown.html) 
has way more information on this, this section is only specific for this project.

You can create a Makefile with some handy commands to aid in the compilation of the document

```shell
# Chain as many --filter as you want

# For bibliography add these flags:
#	--citeproc
#	--bibliography <bibliography file>
# NOTE: --citeproc must come before --filter pandoc-crossref

# For referencing every type of element use --filter pandoc-crossref
pdf:
    pandoc file.md\
	  -so out.pdf\
	  --metadata-file <metadata-file>\
	  --template <template-file>\
	  --filter <filter-name>\
```

## Pandacro filter

This filter is a wrapper for the latex acro package.

To declare the acronyms add the following to the template:

```tex
$if(acronyms)$
    $for(acronyms)$
        \DeclareAcronym{$acronyms.id$}{
            short=$acronyms.short$,
            long=$acronyms.long$
        }
    $endfor$
$endif$
```

Right before the `\end{document}` the acronyms should be printed:

```tex
$if(acronyms)$
	\printacronyms
$endif$
```

To add an acronym to the list you just have to add an entry to the metadata in the following format:

```yml
acronyms:
  - id: the text to be replaced
    short: the acronym's short form
    long: the acronym's long form
  - id: another acronym's id
    short: ...
```

To add a filter you just have to write it's `id`, it will be replaced by `\ac{id}`.

### TODO

- Add support for more acronym types
- Test for punctuation
- Acro configuration via metadata