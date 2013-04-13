# Compiler support

In order to support all kinds of container and tuple types, I make heavy use of template metaprogramming techniques.  This may break some compilers, although it works fine in modern versions of g++ and VC++.

FIXME - make chart of compiler support

NOTE: tell which ones support C++11.

# Windows

On Linux, the "-persist" option can be passed to gnuplot in order to prevent the gnuplot window from closing when your program exits.  On Windows this doesn't seem to work.  The `pgnuplot` command keeps the window open, but temporary files can disappear before they are read.  If you can find a good solution for Windows, let me know.

One thing that will work is to have your program wait for a keystroke before exiting.
