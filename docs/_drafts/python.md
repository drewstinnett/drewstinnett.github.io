---
layout: post
title:  "Bootstrap Your Python Scripts"
date:   2021-01-23 11:26:42 -0500
categories: python sysadmin
---

What is your process for writing a great script?  What I did for many years,
was repeat this process for each script I wrote

* I need a super simple script, lemme write this bash 3-liner

* Actually, it needs to be a little more feature rich, but bash is still
fine…

* Hmmm…ok, gotta add some arguments to this script to make it more
modular…bash argument parsing really sucks…

* Ugh, I wish I started this in python…how about we restart using python

* Ok, what's the syntax for python argparse again?

* Almost there, but debug logging options in this script would be great…lemme
look that up…

I spent lots of churn on most scripts going through this process, so I
naturally started trying to learn from this, and cut down on the steps needed
to get my script ready in a more timely fashion.  I ended up doing this using a
couple techniques, so now as soon as I type `vim my-cool-new-script.py`, all
the boring and repetitive stuff is done!

## Determine the base you want your python scripts to start from

For me, this ended up being a couple things

```python
#!/usr/bin/env python3
import sys
import argparse
import logging


def parse_args():
    """Parse the arguments."""
    parser = argparse.ArgumentParser(
        description="This is my super sweet script")
    parser.add_argument("-v",
                        "--verbose",
                        help="Be verbose",
                        action="store_true",
                        dest="verbose")

    return parser.parse_args()


def main():
    args = parse_args()
    if args.verbose:
        logging.basicConfig(level=logging.DEBUG)
    else:
        logging.basicConfig(level=logging.INFO)
    logging.debug("Starting up...")
    return 0


if __name__ == "__main__":
    sys.exit(main())
```

What do we get with the starter script above?

### Basic argparse

This is the standard python library for argument parsing.  We are giving the
script a default description, as well as a flag for verbose logging.  If you
haven't used [argparse](https://docs.python.org/3/library/argparse.html), check
it out, it is going to make your scripts much easier to use.  Even if you don't
need any arguments, this will give you a nice description of your script when
your users run `script.py -h`.

```
    parser = argparse.ArgumentParser(
        description="This is my super sweet script")
```

### Pythonic Main

Most beginners start python scripts without a `main()` function, treating the
script like an enhanced shell script.  While that works, a more python way to
write a script uses the idiom:

```python
def main():
    return 0

if __name__ == "__main__":
    sys.exit(main())
```

Using the above style structure will allow you to import your `main()` function
in to other scripts, without executing it right away.

Don't take my word for it though, I'm stealing it from the creator of Python,
Guido Van Rossum.  He has a great blog post about it
[here](https://www.artima.com/weblogs/viewpost.jsp?thread=4829)

### Sane Logging

All scripts should have some sort of logging, even if it's not enabled by
default.  This helps track down the issues that will inevitably come up with
either your script, or the thing your script is touching.  I like to log as
much as possible, but keep it under 'DEBUG', so it only actually displays the
log when I run with `-v`/`--verbose`.  This is accomplished with the block:

```python
    if args.verbose:
        logging.basicConfig(level=logging.DEBUG)
    else:
        logging.basicConfig(level=logging.INFO)
```

## Automate it!

OK, we now have a skeleton for our python scripts, but how do we get our new
scripts to start with that already populated?  I use the
[vim-skeleton](https://github.com/noahfrederick/vim-skeleton) plugin.  It's
very simple, but works great!  Install it using your favorite VIM plugin
manager. Once it's installed, place a skeleton version of our basic script in
`~/.vim/templates/skel.py`. Your skeleton script will look like this:

```python
#!/usr/bin/env python3
import sys
import argparse
import logging


def parse_args():
    """Parse the arguments."""
    parser = argparse.ArgumentParser(
        description="This is my super sweet script")
    parser.add_argument("-v",
                        "--verbose",
                        help="Be verbose",
                        action="store_true",
                        dest="verbose")

    return parser.parse_args()


def main():
    args = parse_args()
    if args.verbose:
        logging.basicConfig(level=logging.DEBUG)
    else:
        logging.basicConfig(level=logging.INFO)
    @CURSOR@
    return 0


if __name__ == "__main__":
    sys.exit(main())
```

The only real change from our actual script above is the `@CURSOR@` line.  That
is going to specify where your cursor will start when you create a new script.
