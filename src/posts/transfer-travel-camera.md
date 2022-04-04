---
slug: "/blog/transfer-travel-camera"
date: "2022-04-04"
title: "Transferring pictures and videos from my phone to my computer with bash"
---

A few days ago I came back from a trip.
On my return I wanted to back them up as soon as possible.
Normally I use [KDE Connect](https://kdeconnect.kde.org/) to transfer files between my phone and computer but this didn't work this time.
Dolphin, the Ubuntu file manager, was just to slow to copy the files with the GUI.
I was able to find the path of my phone with Dolphin though so decided to try it with `bash`.

## Regular expressions approach

The first idea I had was to use regular expressions.
All filenames of photos and video contain the date they were taken. 
Using the approach outlined [in this StackOverflow post](https://superuser.com/a/441427) I thought it should work.

In this approach you have to specify the following:
- the location to start your search
- a regular expression matching the files you want to transfer
- the command to execute for each matching result

So I came up with something like this:

```bash
find . -iregex "./(IMG|VID)_2022(0324)?...(0402)?_.*"
```

You run this within the folder on your phone.
The `(0324)?...(0402)?` part refers to the time range I was on holiday.
I could make this regex work when I tested it out on [RegExr](https://regexr.com/) but somehow it didn't when run in the terminal.
After quite some time of experimenting I figured it has to have something to do with escaping characters but just couldn't pinpoint the exact cause.

## Modification time approach

To know when a picture or video was taken it's also possible to look at the last modification time of a file instead of the date in the filename itself.
This way we can leverage the `mtime` argument of the `find` command.
You have to specify the [number of days to go back](https://unix.stackexchange.com/a/112407).

```bash
find . -mtime -11
```

That solves the finding the correct files to transfer already but now we want to do something with them.
That's where `xargs` comes in.
[xargs](https://man7.org/linux/man-pages/man1/xargs.1.html) is the plumbing between the `find` and `cp` command.
It creates commands from the `find` output.


```bash
find . -mtime -11 | xargs -I{} cp {} ~/destination/folder/
```

The `-I` argument is necessary so it replaces `{}` with the standard input (the filename of the file to copy in our case.