# Compiler support

In order to support all kinds of container and tuple types, I make heavy use of template metaprogramming techniques.  This may break some compilers, although it works fine in modern versions of g++ and VC++.

FIXME - make chart of compiler support

NOTE: tell which ones support C++11.

# Windows

On Windows, you may need to be careful to properly quote the path:
	Gnuplot gp("\"C:\\my path\\gnuplot.exe\"");

On Linux, the "-persist" option can be passed to gnuplot in order to prevent the gnuplot window from closing when your program exits.  On Windows this sometimes doesn't work.  When the `Gnuplot` object goes out of scope, the pipe is closed and `gnuplot` exits.  This can be cured by instead using `pgnuplot`, which is for some reason considered to be deprecated.  Perhaps better is to prompt the user to press a key before exiting your program (and before letting the `Gnuplot` object go out of scope).

Sending data to `gnuplot` or `pgnuplot` via stdin is very slow and the data seems to get garbled.  You can get around this by using [temporary files](StdinVsTemporary).  But when the `Gnuplot` object goes out of scope, the temporary files are removed, and this can happen before `gnuplot` gets a chance to read them.  Again the solution is to wait for a keystroke before letting the `Gnuplot` object go out of scope.

If you have any suggestions for Windows please let me know!
