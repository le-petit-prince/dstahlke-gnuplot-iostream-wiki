# Compiler support

In order to support all kinds of container and tuple types, I make heavy use of template metaprogramming techniques.  This may break some compilers, although it works fine in modern versions of g++ and VC++.

C++11 datatypes are not yet supported on VC++, even with the Nov 2012 CTP compiler (although I swear it was working at one point!)

FIXME - make chart of compiler support

NOTE: tell which ones support C++11.

# Windows

On Windows, you may need to be careful to properly quote the path:
	Gnuplot gp("\"C:\\my path\\gnuplot.exe\"");

On Linux, the "-persist" option can be passed to gnuplot in order to prevent the gnuplot window from closing when your program exits.  On Windows this sometimes doesn't work.  When the `Gnuplot` object goes out of scope, the pipe is closed and `gnuplot` exits.  This can be cured by instead using `pgnuplot`, but that is deprecated and seems to garble data.  Perhaps better is to prompt the user to press a key before exiting your program (and before letting the `Gnuplot` object go out of scope).

You can perhaps get better results (faster and less chance of data being corrupted) by using [temporary files](StdinVsFiles).  But when the `Gnuplot` object goes out of scope, the temporary files are removed, and this can happen before `gnuplot` gets a chance to read them.  Again the solution is to wait for a keystroke before letting the `Gnuplot` object go out of scope.

Binary data files (temporary or permanent) work.  Sending binary data to stdin, or inlining binary data into a script, doesn't work.  I suspect that this has to do with CRLF issues.

If you do not require interactive use, it may be better to just write a script using
	Gnuplot gp(">script.gp");
and then pass that to gnuplot manually.

If you have any suggestions for Windows please let me know!
