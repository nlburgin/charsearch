# charsearch

Small Linux app to search for Unicode characters by name and copy them to the clipboard, using a shell script with Zenity.

## System Requirements

First, it's assumed that Zenity is already installed. Zenity is a program that allows GTK+ dialog boxes to be used from shell scripts.

It's also assumed that you're using the usual GNU base system that most Linux systems have. Alternatives like BSD or Busybox have not been tested. While the shell syntax itself is totally standard (so using Dash instead of Bash on Debian/Ubuntu is supported), I'm not sure whether or not I might still be using GNU-specific features of `grep` and/or `sed`.

It's also assumed that you're using the X11 graphics stack. Wayland has not been tested; it may or may not work depending on how good the clipboard integration with Xwayland is.

## Installation

The shell script, '`charsearch`', and the data file, '`codepoint_sorted_by_name.txt`', must both be installed into `/usr/local/bin`. The script needs its executable bit set. So you would run something like

```sh
$ sudo cp --target-directory=/usr/local/bin charsearch codepoint_sorted_by_name.txt
$ sudo chmod +x /usr/local/bin/charsearch
```
After this, you might want to set up a keyboard shortcut in your window manager or desktop environment to bring it up quickly (I prefer WinKey + U). The exact method to accomplish this may vary; as an Xfce user I used Xfconf Settings Editor.

## Usage

When run, the `charsearch` script will produce a dialog box asking for your search terms. Enter them and confirm with the OK button or Enter/Return key, and a list of matches will come up. Double clicking one will copy the character to the X11 clipboard and exit. It can then be pasted into a text entry in a web browser or anything else that supports pasting from clipboard.

## Background Info

The data file is current as of Unicode 12.1. 

Only the "main" name is used, not any aliases. Characters without non-alias names are omitted. Certain other characters with names not considered of interest for my purposes were also omitted.

It was produced with the following Shell pipeline command:

```bash
grep '<char' <ucd.all.flat.xml | sed '/na=""/d' | sed 's/^\s*<char cp="//' | sed 's/"\sage=.*\sna="/ /' | sed 's/"\sJSN=.*$//'| sed '/#/d'| sed '/VARIATION\sSELECTOR/d' | sed '/ACTIVATE/d' | sort -dk 2 | sed 's/^/0x/' | ./hex-to-unicode-filter.py  > codepoint_sorted_by_name.txt
```

I'm sure there's a more elegant way to do that, but it worked, anyway.

The '`ucd.all.flat.xml`' file mentioned in the command can be found [here](https://www.unicode.org/Public/12.1.0/ucdxml/) in the '`ucd.all.flat.zip`' archive. It is the property of the Unicode Consortium, and its license is listed [here](https://www.unicode.org/license.html)

The `hex-to-unicode-filter.py` command can be found [here](https://gist.github.com/nlburgin/173052d91a4f47d9e65e39573fb44464). It's not using the same license as the rest of this project, you can consider it public domain if you want it.

