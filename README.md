# Base16 Konsole

This is a [base16 template repository][tr] to produce `.schema` files for konsole that comes with KDE3.  For newer versions of konsole, see [base16-konsole][ktr].

The files in `schema-vim` will work with [base16-vim][bv].  Only use these if you use KDE3 (RHEL5).

[bv]: https://github.com/chriskempson/base16-vim
[tr]: https://github.com/chriskempson/base16#template-repositories
[ktr]: https://github.com/cskeeters/base16-konsole

# Installation

To install system-wide, copy the desired files (not the folder) into the appropriate folder.  You can download and install only one file if you know what theme you want to use.

Available To |Location
-------------|---------------------------
System Wide  | `/usr/share/apps/konsole/`
User Only    | `~/.kde/apps/konsole/`

You must restart konsole, then you can select the theme you would like to use through the menus.

## Vim Configuration

Follow the instructions for [installation of base16-vim][bvi].  **Do not set base16colorspace!**

[bvi]: https://github.com/chriskempson/base16-vim#installation

### Airline

In RHEL6, vim shows my airline status with bright yellow forground colors instead of darker colors.  It's using `ctermfg=10`, but 10 seems to be set correctly.  I'm not sure why, but changing term=bold to term=NONE fixes the issue.  The easiest way to do this is to remove `bold` from the string value on [this line][airline-bold].

[airline-bold]: https://github.com/vim-airline/vim-airline/blob/45d77ca90953e191e4ac140b964683c2aecef069/autoload/airline/themes.vim#L51

### junegunn/fzf

If you use [fzf][fzf] and want colorization to look right, you'll need something like this in your `.bashrc`.

```sh
if [[ $TERM =~ konsole.* ]]; then
    export FZF_DEFAULT_OPTS='--color fg+:5,hl+:6'
fi
```

This is due to fzf using intense versions of colors.  Since most are dark, the selected item winds up being hard to see.  With `FZF_DEFAULT_OPTS` as mentioned, the selected item will be white and matching text will be orange.  Those are the only good colors to use.

[fzf]: https://github.com/junegunn/fzf

### ls colorization

With kde4-konsole-vim, `ls --color` shows many items in a dark color that is hard to see.  To correct this, you need to set the `LS_COLORS` environment variable to not use bright intensity colors.  You can do this most easily with `dircolors`.  See the [dir_colors manpage][dir_colors] for additional details.  The process for removing intensity code (01) from `LS_COLORS` is:

1. Copy /etc/DIR_COLORS to ~/.dir_colors
1. Edit ~/.dir_colors and modify entries to remove `01;`.  For example, `DIR 01;34` should become, `DIR 34`.
1. Logout and log back in, or run `eval $(dircolors ~/.dir_colors)`

A [base16-vim compatible file][dcgist] is available for you to download if you don't need any further customization.

```sh
cd ~
curl -OL https://gist.githubusercontent.com/cskeeters/aacd10c075d3c7092a5e4e36db34e62d/raw/.dir_colors
```

[dir_colors]: https://linux.die.net/man/5/dir_color://linux.die.net/man/5/dir_colors
[dcgist]: https://gist.github.com/cskeeters/aacd10c075d3c7092a5e4e36db34e62d

### Mercurial Configuration

If you use the [color extension][hgc] for hg, you'll need to customize the colors so that bold is not used.  Add this to your `~/.hgrc`.

    [color]
    status.modified = blue
    status.added = green
    status.removed = red
    status.deleted = cyan
    status.unknown = magenta
    status.ignored = yellow

    diff.extended = cyan
    diff.file_a = red
    diff.file_b = green
    diff.trailingwhitespace = red_background

    qseries.applied = blue underline
    qseries.unapplied = yellow
    qseries.missing = red

[hgc]: https://www.mercurial-scm.org/wiki/ColorExtension

### Advanced Configuration

If you are like me and want a single `.vimrc` to function for konsole (in 16 color mode) and another terminal which supports 256 colors, you can configure konsole's environment to set `TERM=konsole` and then only set base16colorspace when TERM does not start with konsole

```vim
if $TERM !~# "konsole.*"
    " As a work around for the following bugs in kde4's konsole:
    "   use the output of 16.colorscheme.rb and don't set base16colorspace.
    "   base-shell script will not be called
    " https://github.com/chriskempson/base16-shell/issues/31
    " https://bugs.kde.org/show_bug.cgi?id=344181
    let base16colorspace=256
endif
```

# Building

Most users won't need to rebuild this repository as the color files you need for konsole are generated and committed to this template repository.

This template repository can only be built with [cskeeters/base16-builder-php](https://github.com/cskeeters/base16-builder-php) with [this fixed width modification](https://github.com/cskeeters/base16-builder-php/commit/f13a88c9a460c9377b8b27d401996e4f41ebf748).  This enables KDE3's schema format to align properly.

# Technical Details

Read further [technical details from base16-konsole][ktrtd].

[ktrtd]: https://github.com/cskeeters/base16-konsole#technical-details
