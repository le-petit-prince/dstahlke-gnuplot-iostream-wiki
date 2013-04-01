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

# `unwrap` option

# stdin vs. temporary files

# Mouse events

# Custom datatypes

# Custom array types

# Compiler support

NOTE: tell which ones support C++11.

# Windows

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
