Excerpt creation from within mpv
================================

The excerpt.lua script allows you to quickly create excerpts
from media files, you just have to set begin and end markers.

(written by Lutz Vieweg)

Rationale / Use Case:
=====================

When you shoot video with your own camera, chances are you will
want to retain only ~ 10% of your recordings for actual use
in an edited, final cut.

Using video editors like kdenlive - see http://kdenlive.org/ -
is great for assembling and post-processing your final cut,
but it sucks when you try to use it with huge 3840 x 2160 x 24fps
files that were recorded at 100 MBit/s - you'll want to reduce
the amount of data you have to store and deal with to those
parts of the raw video footage you really need.

Now mpv is a marvellous media player, and for me it is currently
the only player that can replay 4k h.264 video files, as
recorded by my Panasonic camera, with little CPU usage and
no stuttering on my laptop.

Also, mpv is the only software that (by utilizing the h.264
decoder hardware well) can very quickly navigate through video
files - the reaction to seek commands is almost instantaneous,
much unlike with many pure software based editors.

For extracting the relevant excerpts from media files it seemed
like an obvious idea to me to script mpv such that I can just
set the "in" and "out" points for a file, then call "ffmpeg"
as an external executable with the appropriate parameters to
seek to the "in" position and copy the content to a new excerpt
file until the "out" position. Without re-encoding, this process
is very quick and does not loose any quality.


Prerequisites / Installation
============================

In order to use excerpt.lua, you'll need to have the executables

* mpv
* ffmpeg 

ready for execution in your $PATH.

(ffmpeg is probably already installed if you have mpv installed,
as mpv makes use of the ffmpeg libraries.)

Copy the "excerpt_copy" shell script to some directory included
in your $PATH. You might want to modify the excerpt_copy script
if you want something else than simple, key-frame accurate extraction
without re-encoding. If you want to change the names of the destination
excerpt files, you have to chang the excerpt.lua script for now.


Usage:
======

First change your working directory to where you want your
video excerpts to be written.

Then just start mpv like this:

mpv --lua /path/to/excerpt.lua -fs --script-opts=osc-layout=bottombar yourmediafile.mp4

Where "yourmediafile.mp4" is of course to be replaced by the file
you actually want to extract from. You can also provide multiple file names,
just press the "Return" key once you're done with a file.

The "-fs" and "--script-opts=osc-layout=bottombar" options are not strictly
required, but IMHO the OSC interface is thus best suited for navigating 
through the files.

After start, the video replay will be paused at the begin.

Use the "left" or "right" cursor keys to seek to the previous/next
frames, and "shift-left" or "shift-right" cursor key to seek to the
previous/next keyframe. You can use the other usual mpv seek features,
too, like the cursor up/down keys, the mouse wheel, the navigation bar
for the mouse etc., and of course you can play/pause pressing the
space bar - whatever you do, just seek to the position where you want your
excerpt to begin.

You can use the "w" and "e" keys to zoom out and in, pressing Ctrl and
left/right/up/down allows to pan around in the zoomed display.

Then press the "i" key to mark the begin (or "in"-position) of the excerpt.

Then seek to the position where you want your excerpt to end, and
press the "o" key to mark the end (or "out"-position) of the excerpt.

You can also seek to the begin or end marks you already set by
pressing shift-i and shift-o.

If you are not satisfied with your begin / end choice, you can
press "i" and "o" again to move the marks to the current position.

Once To actually write the excerpt, press "x".
The excerpt.lua script will create a filename that does not
yet exist in your current working directory, files will be
named "excerpt_000.mp4", "excerpt_001.mp4" etc. by default,
and no existing files will be overwritten.

The actual extraction work is done by invoking the "extract_copy"
shell script, which is given the source file name and the "in"
and "out" position as parameters. The supplied extract_copy
script will use ffmpeg to create the extract, by default without
any re-encoding, and therefore only pricise to the keyframe.
If you want frame-exact extraction, you will have to tell ffmpeg
to re-encode your media file, which of course takes much longer,
and might also lower the quality. So if you plan on post-processing
the excerpts in another software (like kdenlive) anyway, don't
bother with frame-exact extraction and fine tune the in/out positions
using the post-processing software.

Options:
========

## Source file name as base for destination file name

If you do not like the default of naming excerpted files "excerpt_000"
to "excerpt_999" (plus filename extension), but would rather like to
use the source file name as base file name for your excerpts, you can
set the script option excerpt-source-based-filename=1 like this:

 mpv --script-opts=excerpt-source-based-filename=1  ...
 
## Source file name extension as the extension for destination file name

If you do not like the default of using ".mp4" as the extension for
excerpted files, which implies the use of MP4 as the output format
when using the supplied excerpt_copy script, you can use the script option
excerpt-source-based-extension=1 like this:
  
 mpv --script-opts=excerpt-source-based-extension=1 ...

Notice that using both options described above at the same time
is possible using comma-separated script options, like this: 

 mpv --script-opts=excerpt-source-based-filename=1,excerpt-source-based-extension=1 ...


DISCLAIMER
==========

This software is provided as-is, without any warranties.

Please notice that it is in an early stage at the moment, there
is probably a lot that can be enhanced.
