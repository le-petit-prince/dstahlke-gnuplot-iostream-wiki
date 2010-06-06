Hello.

Things I like to add the wiki:

We should have a page describing how to use the different data container (STL, Blitz).
Since this is a low level library you need to be familiar with gnuplot. See tests/examples for using data from STL containers and Blitz arrays.

FAQ

How do I start an interactive portable gnuplot?

Don't set the terminal manually. Your system should provide a reasonable default.
See section "75.64 Terminal" of the gnuplot 4.4 manual. It says: "After gnuplot's startup, the default terminal or that from startup le is pushed automatically."

Does zooming work in the gnuplot window?

Depends. Starting with version 4.4 it should work out of the box.

Why does the gnuplot window closes directly after displaying?

It should work with:
Gnuplot gp("gnuplot --persist")


How do I save the gnuplot file?

You can save the plot file with:
Gnuplot gp("cat > plot.gpi")

You can plot the file and save it:
Gnuplot gp("tee plot.gpi | gnuplot")

This should work under any Unix-based operating system.

Does gnuplot iostream work on Windows?

We don't know. We haven't tested it on Windows but are willing to get it working with the help of some Windows user.


My question is not in the FAQ.

Please file a bug via mail/bug tracker.

