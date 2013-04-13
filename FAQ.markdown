# Why does the gnuplot window closes directly after displaying?

On Unix systems it should work with:
	Gnuplot gp("gnuplot -persist");

On Windows this doesn't seem to work.  The `pgnuplot` command keeps the window open, but temporary files can disappear before they are read.  If you can find a good solution for Windows, let me know.

One thing that will work is to have your program wait for a keystroke before exiting.

# How do I save the gnuplot file?

You can save the plot file with:
	Gnuplot gp(fopen("script.gp", "w"));

On Unix systems (or, possibly, with Cygwin) you can simultaneously plot and save using
	Gnuplot gp("tee script.gp | gnuplot")

# My question is not in the FAQ.

Please email `dan@stahlke.org`.
