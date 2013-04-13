# Why does the gnuplot window closes directly after displaying?

On Unix systems it should work with:
	Gnuplot gp("gnuplot -persist");
Although, currently that is the default anyway, so it should work right out of the box.

On Windows there are issues.  See the [portability](Portability) page for hints.

# How do I save the gnuplot file?

You can save the plot file with:
	Gnuplot gp(fopen("script.gp", "w"));
Or,
	Gnuplot gp(">script.gp");

On Unix systems (or, possibly, with Cygwin) you can simultaneously plot and save using
	Gnuplot gp("tee script.gp | gnuplot -persist")

Note that if `Gnuplot` in constructed with no arguments then you can set the `GNUPLOT_IOSTREAM_CMD` environment variable.  For example, on Linux do
	GNUPLOT_IOSTREAM_CMD=">script.gp" ./my_program

# My question is not in the FAQ.

Please email `dan@stahlke.org`.
