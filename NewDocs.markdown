# Intro

# Basic usage

## Quick example

```c++
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
```

## Construction

## `iostream` interface

## Sending data

## Demos

# Legacy interface

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
