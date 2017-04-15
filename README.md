# CLI Press

A simple CLI script to turn a directory of Markdown files into a nice PDF with a 
**Table of Contents** (with working links), an optional cover page, and personalized style.  It comes with a basic theme which can be extended/overwritten using standard CSS.  Check out the docs for `cli-press` created using `cli-press` [here](CLI Press.pdf).

# Requirements

**CLI Press** uses [wkhtmltopdf](http://wkhtmltopdf.org) to generate the PDF from standard HTML/JavaScript/CSS files.  It also uses a Markdown parser, so you can create your files using a mixture of Markdown, CSS, and HTML.  

# Installation

```
composer require --global blazeworx/cli-press
```

This will install the `cli-press` script as a global requirement and add it to composer's *bin* folder.  Be sure this *bin* folder (usually found in `~/.composer/vendor/bin`) is in your `PATH` environment variable for easiest use.

<div class="break"></div>

# Usage

The simplest usage is simply to call the script in a directory with Markdown files using the `generate` command.

```
cli-press generate
```

The script will search for all files with the **.md** extension and generate a PDF.  Because all files are globbed together in file-system sort order, it's best to name them in such a way that they will be processed in the order you intend.

## `cli-press.json`

By default, `cli-press` will use **Generated by CLI Press** as the document title in the header of each page.  Also by default, the generated PDF will be called *cli-press.pdf*.  To override these defaults, place a `cli-press.json` file in the directory.  This file must be standard JSON and may contain any of the following keys: `filename`, `title`, `theme`.  As you may have guessed, you can override the default title and PDF filename by specifying values for the `title` and `filename` keys.

If you only specify a `title`, the filename will be a slugged version of that value.  For example, the following `cli-press.json` file:
  
```
{
    "title": "My Awesome API v1.0beta"
}
```

would use **My Awesome API v1.0beta** in the header on each page and would produce a PDF named *My-Awesome-API-v1.0beta.pdf*.

# Themes

The PDF is styled using CSS for the header, footer, body, and cover sheet, and by using an XSL file for the **Table of Contents**.  You can add a theme to your installation of **CLI Press** by creating a folder in *./src/themes* (relative to the composer vendor folder where the installation lives).  There is an existing folder called **cli-press** where the base CSS files can be found.

Simply create CSS file(s) with the same name(s) as the one(s) you want to override/extend in your theme folder.  Then in the `cli-press.json` file, specify the `theme` key and provide the name of the theme folder.  When `cli-press` runs it will load both the base CSS file and any theme file with the same name.

For more fine-grained control, you can also override the default layouts for the header and footer.  Both of these items are created by processing a pair of `phtml` files within the theme folder.  The *header-layout.phtml* and *footer-layout.phtml* files contain the `head`, `style`, and `body` tags to style the header and the footer.  The *header.phtml* and *footer.phtml* files contain the markup to display the header and the footer.  Unlike the CSS files, with these files the `cli-press` script will only use a file from your theme or the base theme.  For instance, if you simply want to make a small change to the default footer, you could place a *footer.phtml* file in your theme folder, and `cli-press` will use that instead of the base theme file.  These files are rendered within a scoped context and will have certain variables pre-defined depending on the file.  Look at the base files to see what they are.

If for some reason you wanted some javascript processing to occur, or you have public CSS assets to use, you can also use the layout files in your theme to specify custom `head` settings.  All of *header-layout.phtml*, *footer-layout.phtml*, and *body-layout.phtml* can be used within a theme.  The same goes for the XSL file used to create the **Table of Contents**.  Again, `cli-press` will only use your file if you specify a theme and the file exists in your theme.  Otherwise, the default file will be used.

For a quick reminder of which themes you have defined, run:

```
cli-press themes
```

<div class="break"></div>

# Optional Cover Page

If there is a *cover.md* file in the directory, `cli-press` will use it's contents to generate a cover page for the PDF.

# Bonus

Do you like Font Awesome?  If so, good news!  You can use some shorthand to put your favorite icons right in your documentation.  Here are some examples:

```
This: {f@chevron-left}

Becomes: <i class="fa fa-chevron-left"></i>

This: {f@search 4x}

Becomes: <i class="fa fa-search fa-4x"></i>

This: {f@shield rotate-270 lg}

Becomes: <i class="fa fa-shield fa-rotate-270 fa-lg"></i>
```

I think you get the picture.  Size, rotation, and flipping are the only supported class transformations.  Currently, if `cli-press` detects any Font Awesome patterns, it will automatically include a CDN of the Font Awesome CSS file for version 4.7, otherwise you will need to do that yourself.