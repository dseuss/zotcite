# Zotcite

Omni completion of citation keys from Zotero database for Markdown and RMarkdown documents.

## Installation

Zotcite can be installed as any Vim plugin. It is recommended the use of a
plugin manager, such as [Vim-Plug](https://github.com/junegunn/vim-plug). It
requires Python 3 and Zotero 5.

The `zotero` module does not import the `vim` module. Hence, its code could
easily be adapted to other text editors such as Emacs or Gedit.

## Use

To insert citation keys, in Insert mode, type the `@` letter and one or more
letters of either the last name of the first author or the reference title and
press `CTRL-X CTRL-O`. The matching of citation keys is case insensitive.

You can also use [citation.vim](https://github.com/rafaqz/citation.vim)
to insert the citation keys if you set the Zotero key to be the first part of
the citation key followed by a hash tag. Example:

```vim
let g:citation_vim_key_format = "{zotero_key}#{author}_{date}"
```

To convert a Markdown document with pandoc, use the `zotref` filter that comes
with Zotcite. The Zotcite plugin adds the directory where `zotref` is to
the system `$PATH`. So, from within Vim/Neovim, do:

```vim
:!pandoc file_name.md -s -o file_name.html -F zotref -F pandoc-citeproc
```

Replace `file_name` with the actual name of the markdown document being
edited, and `html` with the appropriate file extension.

To compile an RMarkdown document with the [Nvim-R](https://github.com/jalvesaq/Nvim-R)
plugin, add the following lines to the YAML header of the document (replace
`pdf_document` with the desired output):

```yaml
output:
  pdf_document:
    pandoc_args: ['-F', 'zotref', '-F', 'pandoc-citeproc']
```

Note: The Windows operating system doesn't know that `zotref` should be run by
Python, and, instead of running `zotref` directly you would have to run a
batch script such as:

```
py C:\Users\yourlogin\vimfiles\plugged\zotcite\python3\zotref
```

That is, a command with the Python 3 executable followed by `zotref` path. If
`zotref` still fails to work properly, please, try adding the references to
the YAML header with `:ZRefs`. Then, if there are only ascii characters in
the references you will have a chance of successfully compiling the document
on Windows.

In Vim's Normal mode, put the cursor over a citation key and press:

  - `<Leader>zo` to open the reference's attachment as registered in Zotero's
    database.

  - `<Leader>zi` to see in the status bar the last name of all authors, the
    year and the title of the reference.

  - `<Leader>za` to see all fields of a reference as stored by Zotcite.

  - `<Leader>zy` to see how the reference will be converted into YAML.

You can also use the command `:ZSeek` to see what references have either a
last author's name or title matching the pattern that you are seeking for.
Example:

```vim
:ZSeek marx
```

The goal of Zotcite is to avoid the need of exporting bib files from Zotero,
but if for any reason you need a bib file with your citation keys, do the
following:

```vim
!zotbib file_name.md file_name.bib
```

Note: `file_name.bib` will be overwritten if it already exists.

## Customization

### Change default maps

To change the shortcut to open reference attachment, set the value of
`<Plug>ZOpenAttachment` in your vimrc as below:

```vim
nmap <c-]> <Plug>ZOpenAttachment
```

To change the shortcut to see basic information on the reference of a
citation key, follow the example:

```vim
nmap ,I <Plug>ZCitationInfo
```

To change the shortcut to see how the reference of a citation key is stored
by Zotcite, follow the example:

```vim
nmap ,A <Plug>ZCitationCompleteInfo
```

To change the shortcut to see how the reference is converted into YAML,
follow the example:

```vim
nmap ,Y <Plug>ZCitationCompleteInfo
```


### Zotcite engine

You can change most of Zotcite's behavior by setting some environment
variables in your `vimrc`/`init.vim` (or in your `~/.bashrc`, if you use bash
as your shell).

The citation keys inserted by Zotcite have the format `@ZoteroKey#Author_Year`
where `ZoteroKey` is the key attributed by Zotero to the references stored in
its database, `Author` is the last name of the first author, and `Year` is the
full year (four digits). The `@ZoteroKey#` part of the citation key is
mandatory because `zotref` uses it to add the bibliographic data that is
parsed by `pandoc-citeproc`, but it will be concealed by Vim. The remaining of
the key is customizable. You can define whether it will include or not the
first author's last name (in either lower case `{author}` or title case
`{Author}`), the first word of the title (`{title}` or `{Title}`) and the year
(with either four digits `{Year}` or only two `{year}`). Examples:

```vim
let $ZCitationTemplate = '{author}{year}'
let $ZCitationTemplate = '{Author}_{Year}_{Title}'
```

The following words are deleted from the title before its first word is
selected: a, an, the, some, from, on, in, to, of, do, and with. If you want to
ban a different set of words, define `$ZBannedWords` in your vimrc as in the
examples:

```vim
" Spanish
let $ZBannedWords = 'el la las lo los uno una unos unas al de en no con para'
" French
let $ZBannedWords = 'la les l un une quelques à un de avec pour'
" Portuguese
let $ZBannedWords = 'o um uma uns umas à ao de da dos em na no com para'
" Italian
let $ZBannedWords = 'la le l gli un una alcuni alcune all alla da dei nell con per'
" German
let $ZBannedWords = 'der die das ein eine einer eines einem einen einige im zu mit für von'
```

If Zotcite cannot find the path to Zotero's database (`zotero.sqlite`), put
the correct path in your vimrc:

```vim
let $ZoteroSQLpath = '/path/to/zotero.sqlite'
```

If Zotcite is trying to use a non writable directory as its temporary and
cache directory, or if for any reason you want to change this directory,
follow the example:

```vim
let $Zotcite_tmpdir = '/path/to/temporary_and_cache_directory
```

If you want to restrict the search for references to specific collections
while completing citation keys, set the value `collection` in the YAML header
of the Markdown document, as in the examples:

```
---
collection: ['New Paper']
...
```

```
---
collection: ['PhD Thesis', 'New Paper']
...
```

### Syntax Highlighting

Syntax highlighting customization is done with Vim variables.

If you want to disable Zotcite's syntax highlighting of citation keys, put in
your vimrc:

```vim
let zotcite_hl = 0
```

Zotcite sets the conceallevel of the Markdown document to 2. If you want a
different value, follow the example:

```vim
let zotcite_conceallevel = 0
```

## Troubleshooting

If either the plugin does not work or you want easy access to the values of
some internal variables, do the following command:

```vim
:Zinfo
```

Note that Zotcite will not enable omni completion if any of the buffer's lines
matches the regular pattern `^bibliography:.*\.bib` when the markdown file is read.

## Acknowledgment

Zotcite code is based on the
[citation.vim](https://github.com/rafaqz/citation.vim) project which provides
[Unite](https://github.com/Shougo/unite.vim) sources for insertion of Zotero
and bibtex citation keys and has many other features.
