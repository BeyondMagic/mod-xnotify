<p align="center">
  <img src="/.github/preview.gif", title="Preview of xnotify build."/>
</p>

<p align="center">
XNotify receives a notification specification in stdin and shows a notification for the user on the screen.
</p>

---

This is a build with the purpose to be solely used with [this lemonbar script](https://github.com/BeyondMagic/scripts/blob/master/bars/).

See the [manual](https://github.com/phillbush/xnotify/wiki) by phillbush (authour) for more information on how xnotify works.


## Features

XNotify comes with the following features:

+ xnotify receives notifications from stdin.
  You can use a fifo to echo notifications on the fly like

  `echo Hello World > /path/to/xnotify.fifo`

+ xnotify queue notifications and display them one above the other.

+ Image support, just prefix the notification string with `IMG:/path/to/the/file.png` and a tab.

+ Multiple monitor support.  You can set the monitor with the `-m` option.

+ Support for fallback fonts (you can set more than one fonts, that will be tried in order).

+ X resources support (you don't need to recompile Xnotify for configuring it).

---

## Files

The files are:

* `./README`:           This file.
* `./Makefile`:         The makefile.
* `./config.h`:         The hardcoded default configuration for XNotify.
* `./config.mk`:        The setup for the makefile.
* `./xnotify.{c,h}`:    The source code of XNotify.

---

## Installation

First, edit `./config.mk` to match your local setup.

In order to build XNotify you need the `Imlib2`, `Xlib` and `Xft` header files.
The default configuration for XNotify is specified in the file `config.h`,
you can edit it, but most configuration can be changed at runtime via
X resources and via command-line options.
Enter the following command to build XNotify.
This command creates the binary file `./xnotify`.

	make

By default, XNotify is installed into the `/usr/local` prefix.  Enter the
following command to install XNotify (if necessary as root).  This command
installs the binary file `./xnotify` into the `${PREFIX}/bin/` directory, and
the manual file `./xnotify.1` into `${MANPREFIX}/man1/` directory.

	make install

---

## Running XNotify

XNotify receives as input one line per notification.
Each line is made out of a notification title and a notification body separated by any number of tabs.
Lines without a title are ignored.

The following is an example of how to run XNotify

	$ xnotify -m 10 -G NE -g -10+10 -s 15

This line means: read notifications from stdin, display
the notifications on the north east (`-G NE`) of the monitor 0 (`-m 0`),
that is, on the upper right corner of the first monitor.  The
notifications should be placed 10 pixels to the left and 10 pixels
down (thus creating a 10 pixel gap with the upper right corner).
Each notification stay alive for 15 seconds.

To create a named pipe for XNotify, you can place the following in the beginning of your `~/.xinitrc`.
This will create a named pipe unique to your current X display in your home directory at `~/.cache`.
Then, it will open xnotify in the background, reading from this named pipe.

	XNOTIFY_FIFO="$HOME/.cache/xnotify$DISPLAY.fifo"
	export XNOTIFY_FIFO
	rm -f $XNOTIFY_FIFO
	mkfifo $XNOTIFY_FIFO
	xnotify 0<>$XNOTIFY_FIFO

Note that the first two lines (the line setting the environment variable and the line exporting it)
should be at the beginning of your `~/.xinitrc`, so other programs you invoke are aware of this variable.

To create a notification with a image, input to XNotify a line beginning
with `IMG:/path/to/file.png` followed by a tab.  For example:

	$ printf 'IMG:/path/to/file.png\tThis is a notification\n' > $XNOTIFY_FIFO

To read dbus notifications from stdin, you'll need [tiramisu](https://github.com/Sweets/tiramisu).
Then add the following line to your `.xinitrc`, after the line calling xnotify.

	$ tiramisu -o "$(printf '#summary\t#body\n')" > $XNOTIFY_FIFO &

To use a different size other than the default for the notifications,
run `xnotify` with the `-g` option set to the notification size in
`WIDTHxHEIGHT`.  For example:

	$ xnotify -g 300x80

The argument for the `-g` option has the form `[WIDTHxHEIGHT][{+-}XPOS{+-}YPOS]`.
Parts between square brackets are optional.
`{+-}` means to chose either `+` or `-`.

---

<p align="center">
  <a href="/LICENSE"><b>MIT/X Consortium License</b></a>
</p>
