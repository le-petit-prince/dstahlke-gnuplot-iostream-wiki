# Construction

You can pass the path to your gnuplot executable, along with any commandline parameters:
	Gnuplot gp("gnuplot -persist");
If you don't pass any parameters, then the enviroment variable `GNUPLOT_IOSTREAM_CMD` is used if set, otherwise a guess is made as to the best command to use for your operating system (usually `gnuplot -persist`).

On Windows, you may need to be careful to properly quote the path:
	Gnuplot gp("\"C:\\my path\\gnuplot.exe\"");

If you pass a `FILE *` then everything is sent there instead of to gnuplot:
	Gnuplot gp(fopen("script.gp", "w"));
Outputting to console is useful for debugging:
	Gnuplot gp(stdout);

Also useful for debugging is to set `GNUPLOT_IOSTREAM_CMD="cat"` to send the commands to console or `GNUPLOT_IOSTREAM_CMD=">script.gp"` to write to a file.

NOTE: Windows support is a bit dodgy.  If you can make it better let me know.  There are some hits on the [Portability](Portability) page.

# `iostream` interface

Commands are sent to gnuplot using the `<<` operator.
	gp << "set xrange [0:1]\n";
Don't forget the newline at the end of each command!

# Sending data

There are several method for sending data, the basic two being `send1d(data)` and `send2d(data)`.  The 1d functions are for things like points and curves.  The 2d functions are for surfaces and images.  This is explained in more detail on the [datatypes page](Datatypes).

The most basic usage is to send the data through gnuplot's stdin, like so:
	gp << "plot '-' with points\n";
	gp.send1d(data);

To send using temporary files:
	gp << "plot" << gp.file1d(data) << "with points\n";

To send using non-temporary files:
	gp << "plot" << gp.file1d(data, "filename.dat") << "with points\n";

You can send data in [binary format](BinaryData) rather than text, and this is probably a bit more efficient:
	gp << "plot" << gp.binFile1d(data, "record") << "with points\n";

# Quick example

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
		// '-' means read from stdin.  The send1d() function sends data to
		// gnuplot's stdin.
		gp << "plot '-' with vectors title 'pts_A', "
			<< "'-' with vectors title 'pts_B'\n";
		gp.send1d(pts_A);
		gp.send1d(boost::make_tuple(pts_B_x, pts_B_y, pts_B_dx, pts_B_dy));
	}

# Demos

There are several examples in the source:

* `example-misc.cc` - Shows basic usage, temporary files, binary files, and various other things.
* `example-interactive.cc` - Demonstrates handling mouse click events.  Currently only works in Linux.
* `example-data-1d.cc` - Demonstration of various ways data can be passed, for 1d data (curves).  This should plot a series of intertwined curves in the shape of a torus, with each curve being passed using a different type of container.  This is meant to demonstrate all the different types of containers you can use to pass your data.
* `example-data-2d.cc` - Similar to the above but for 2d data.  Should plot a segmented torus, with each segment corresponding to a different type of container.
