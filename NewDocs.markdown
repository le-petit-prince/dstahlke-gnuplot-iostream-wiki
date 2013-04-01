# Intro

This interface allows gnuplot to be controlled from C++ and is designed to
be the lowest hanging fruit.  In other words, if you know how gnuplot works
it should only take 30 seconds to learn this library.  Basically it is just
an iostream pipe to gnuplot with some extra functions for pushing data
arrays and getting mouse clicks.  Data sources include STL containers (eg.
vector), Blitz++, and armadillo.  You can use nested data types like
`std::vector<std::vector<std::pair<double, double>>>` (as well as even more
exotic types).  Support for custom data types is possible.

This is a low level interface, and usage involves manually sending commands
to gnuplot using the `<<` operator (so you need to know gnuplot syntax).
This is in my opinion the easiest way to do it if you are already
comfortable with using gnuplot.  If you would like a more high level interface
check out the [gnuplot-cpp library](http://code.google.com/p/gnuplot-cpp).

If you have any questions, bugs, or suggestions, email me at `dan@stahlke.org`.

# Basic usage

## Construction

You can pass the path to your gnuplot executable, along with any commandline parameters:
	Gnuplot gp("gnuplot -persist");
The `-persist` option prevents the gnuplot window from closing when your program exits, at
least on Linux.  On Windows it's a bit trickier and I don't really have a good solution yet
(although you can just make your program wait for a keystroke before exiting).
On Windows, you need to be careful to properly quote the path:
	Gnuplot gp("\"C:\\my path\\gnuplot.exe\"");
The default constructor, with no arguments, is the same as `Gnuplot gp("gnuplot")`.

If you pass a `FILE *` then everything is sent there instead of to gnuplot:
	Gnuplot gp(fopen("script.gp", "w"));
Outputting to console is useful for debugging:
	Gnuplot gp(stdout);

## `iostream` interface

Commands are sent to gnuplot using the `<<` operator.
	gp << "set xrange [0:1]\n";
Don't forget the newline at the end of each command!

## Sending data

There are several method for sending data, the basic two being `send1d(data)` and
`send2d(data)`.
The 1d functions are for things like points and curves.  The 2d functions are for surfaces and
images.
This is explained in more detail in later sections.

The most basic usage is to send the data through gnuplot's stdin, like so:
	gp << "plot '-' with points\n";
	gp.send1d(data);

To send using temporary files:
	gp << "plot" << gp.file1d(data) << "with points\n";

To send using non-temporary files:
	gp << "plot" << gp.file1d(data, "filename.dat") << "with points\n";

You can data in binary format rather than text, and this is probably a bit more efficient:
	gp << "plot" << gp.binFile1d(data, "record") << "with points\n";

## Quick example

	// Compile it with:
	//   g++ -o example-vector example-vector.cc -lboost_iostreams -lboost_system -lboost_filesystem

	#include <vector>
	#include <cmath>
	#include <boost/tuple/tuple.hpp>

	#include "gnuplot-iostream.h"

	int main() {
		Gnuplot gp("gnuplot -persist");

		// Gnuplot vectors (i.e. arrows) require four columns: (x,y,dx,dy)
		std::vector<boost::tuple<double, double, double, double> > pts_A;

		// You can also use a separate container for each column, like so:
		std::vector<double> pts_B_x;
		std::vector<double> pts_B_y;
		std::vector<double> pts_B_dx;
		std::vector<double> pts_B_dy;

		// Make the data
		for(double alpha=0; alpha<1; alpha+=1.0/24.0) {
			double theta = alpha*2.0*3.14159;
			pts_A.push_back(boost::make_tuple(
				 cos(theta),
				 sin(theta),
				-cos(theta)*0.1,
				-sin(theta)*0.1
			));

			pts_B_x .push_back( cos(theta)*0.8);
			pts_B_y .push_back( sin(theta)*0.8);
			pts_B_dx.push_back( sin(theta)*0.1);
			pts_B_dy.push_back(-cos(theta)*0.1);
		}

		// Don't forget to put "\n" at the end of each line!
		gp << "set xrange [-2:2]\nset yrange [-2:2]\n";
		// '-' means read from stdin.  The send1d() function sends data to gnuplot's stdin.
		gp << "plot '-' with vectors title 'pts_A', '-' with vectors title 'pts_B'\n";
		gp.send1d(pts_A);
		gp.send1d(boost::make_tuple(pts_B_x, pts_B_y, pts_B_dx, pts_B_dy));
	}

## Demos

There are several examples in the source:

* `example-misc.cc` - Shows basic usage, temporary files, binary files, and various other
  things.
* `example-interactive.cc` - Demonstrates handling mouse click events.  Currently only works in
  Linux.
* `example-tuples.cc` - Demonstration of various ways data can be passed, for 1d data (curves).
  This should plot a series of intertwined curves in the shape of a torus, with each curve
  being passed using a different type of container.
* `example-uv.cc` - Similar to the above but for 2d data.  Should plot a segmented torus, with
  each segment corresponding to a different type of container.

# Legacy interface

The functions `send`, `binfmt`, `sendBinary`, `file`, and `binaryFile` are deprecated.  Don't
use them.  Here's the problem: you can send an array of columns of data using a datatype
like `std::vector<std::vector<double>>`.  But is it the first index or the second that
corresponds to the columns?  And how to know whether it should be an array of columns or a grid
of scalars?  In earlier versions of this library only a few datatype were supported and ad-hoc
rules were assigned to interpreting each one.  Now a much wider range of datatypes are
supported, and this way no longer makes sense.  Now you have to explicitly tell whether the
data is 1d (for points/curves) or 2d (gridded data like surfaces), and whether the first index
or the last corresponds to the columns.  The old methods still work, and the ad-hoc rules have
been extended but these should not be used.

# 1d vs. 2d data

All of the data sending commands come in 1d and 2d variants (e.g. `send1d()` and `send2d()`).
1d data is for points and curves (basically anything you would use the `plot` command for).
2d data is for images and surfaces (basically anything sent to `splot`).

If you send a 3x2 dimensional `std::vector<std::vector<double> >` to `send1d()` it will send
something that looks like
	1 2
	3 4
	5 6
whereas `send2d()` would send
	1
	2

	3
	4

	5
	6

# Container datatypes

The following array-like containers are supported:
* STL containers (e.g. `std::vector`), as well as anything with a similar iterator interface.

nested containers

tuples are columns

FIXME - under construction

# `colmajor` option

You can store an array of columns in a `std::vector<std::vector<double> >`.
Logically, considering the C convention for ordering indices, the last index should correspond
to the column.  So if the array contained the data `[[1,2],[3,4],[5,6]]` then `send1d()` would
send
	1 2
	3 4
	5 6
However, considering that the number of columns is typically much smaller than the number of
rows, it is more efficient when using a `std::vector<std::vector<double> >` to have the first
index correspond to the column.  If you store your data that way then use `send1d_colmajor()`.
The `[[1,2],[3,4],[5,6]]` data would then be sent as
	1 3 5
	2 4 6

There is a `send2d_colmajor()` for multiple column gridded data.
The binary senders also have `*_colmajor()` variants.
There are examples of both 1d and 2d colmajor commands in `example-tuples.cc` and
`example-uv.cc`.

There is no need to use colmajor for things like
`std::pair<std::vector<double>, std::vector<double>>`.
Anything tuple-like (`std::pair`, `std::tuple`, `boost::tuple`, `blitz::TinyVector`) is always
associated with columns.  The colmajor option applies to the outermost non-tuple container.

# stdin vs. temporary files

Data can be passed through gnuplot's `stdin` like so
	gp << "plot '-' with lines\n";
	gp.send1d(data);
or can be passed via a temporary file,
	gp << "plot" << gp.file1d(data) << "with lines\n";
or even a non-temporary file,
	gp << "plot" << gp.file1d(data, "file.dat") << "with lines\n";

Binary data can also be passed via stdin or files.

Each has advantages and disadvantages.

* Passing through `stdin` avoid having to create temporary files.
  The disadvantage is that if something goes wrong, gnuplot can start interpreting your data as
  commands.  This tends to spew a lot of error messages onto the console.
* Temporary files could possibly be left behind if your program crashes before cleanup can be
  done.
* If you are rapidly plotting data, as in an animation, you might not want to be creating
  hundreds of temporary files.
* If your program runs and exits quickly, the temporary files might be cleaned up before
  gnuplot can read them.  The `pgnuplot` program on Windows seems especially prone to this
  problem.
* If you are generating a script, using `Gnuplot gp(fopen("script.gp", "w"))` then it may make
  more sense to put the data in separate files rather than inlining it into your script.

# Mouse events

On Linux you can intercept mouse clicks.  The coordinates as well as the button number are
reported.  See `example-interactive.cc` for an example.

# Custom datatypes

If you define your own custom data type (e.g. a `struct`) then you must tell gnuplot-iostream
how to print it.  This is done by providing a specialization of the `TextSender` class (for
methods like `send1d()` that send text) or the `BinfmtSender` and `BinarySender` classes (for
methods like `sendBinary1d()` that send binary data).  For an example, take a look at
`example-tuples.cc`.

If there is no specialization of `TextSender` then the `<<` operator is used.  So you can also
just provide a specialization of `operator<<(std::ostream &, ...)`.

# Custom array types

STL containers, Armadillo, and Blitz++ are supported.  Support for other containers is added by
providing a specialization of the `ArrayTraits` class.  For an example, look at the Armadillo
and Blitz++ portions of `gnuplot-iostream.h`.

# Compiler support

FIXME - make chart of compiler support

NOTE: tell which ones support C++11.

# Windows

On Linux, the "-persist" option can be passed to gnuplot in order to prevent the gnuplot window
from closing when your program exits.  On Windows this doesn't seem to work.  The `pgnuplot`
command keeps the window open, but temporary files can disappear before they are read.  If you
can find a good solution for Windows, let me know.

One thing that will work is to have your program wait for a keystroke before exiting.

# FAQ

## Why does the gnuplot window closes directly after displaying?

On Unix systems it should work with:
	Gnuplot gp("gnuplot -persist")

On Windows, `pgnuplot` may work better, but temporary files can disappear before `pgnuplot`
reads them.  If you know the best way in Windows, please let me know.

## How do I save the gnuplot file?

You can save the plot file with:
   Gnuplot gp(fopen("script.gp", "w")

## My question is not in the FAQ.

Please email `dan@stahlke.org`.
