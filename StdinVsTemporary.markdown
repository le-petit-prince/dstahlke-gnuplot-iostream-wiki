# stdin vs. temporary files

Data can be passed through gnuplot's `stdin` like so
	gp << "plot '-' with lines\n";
	gp.send1d(data);
or can be passed via a temporary file,
	gp << "plot" << gp.file1d(data) << "with lines\n";
or even a non-temporary file,
	gp << "plot" << gp.file1d(data, "file.dat") << "with lines\n";

[Binary data](BinaryData) can also be passed via stdin or files.

Each has advantages and disadvantages.

* Passing through `stdin` avoid having to create temporary files.  The disadvantage is that if something goes wrong, gnuplot can start interpreting your data as commands.  This tends to spew a lot of error messages onto the console.
* Temporary files could possibly be left behind if your program crashes before cleanup can be done.
* If you are rapidly plotting data, as in an animation, you might not want to be creating hundreds of temporary files.
* If your program runs and exits quickly, the temporary files might be cleaned up before gnuplot can read them.  The `pgnuplot` program on Windows seems especially prone to this problem.
* If you are generating a script, using `Gnuplot gp(fopen("script.gp", "w"))` then it may make more sense to put the data in separate files rather than inlining it into your script.

Temporary files will automatically be erased when your program exits, or when the `Gnuplot` object goes out of scope.  If you wish to manually wipe them at any time, just call `gp.clearTmpfiles()`.  Be warned that calling `gp.clearTmpfiles()` just after a plot command can cause trouble since the files can be removed before gnuplot has a chance to read them!
