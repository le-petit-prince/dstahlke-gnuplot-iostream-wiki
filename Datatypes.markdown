# Overview

You can provide data to the `send1d()` or `send2d()` (or other similar) commands using just about any type of container you want.  You can even nest several different types of containers.

The rules for interpreting your data are as follows.  All tuple datatypes (such as `std::pair` or `boost::tuple`) correspond to columns.  For example, the datatype

	std::pair<std::vector<double>,std::vector<std::pair<int,float>>>

is interpreted as a one dimensional array with three columns, of types `double`, `int`, and `float`.  Containers can be nested several layers deep to create a multidimensional array, like `std::vector<std::vector<double>>`, although you might be better off using Armadillo or Blitz++ for multidimensional arrays.

If you use `send1d()` then the first axis of this array corresponds to records and any further axes correspond to columns.  If you use `send2d()` then the first two axes correspond to blocks of records (for plotting two dimensional data like images or surfaces) and any remaining axes correspond to columns.  If you use `send1d_colmajor()` or `send2d_colmajor()` then the first axis corresponds to columns, and the remaining axes are interpreted using the rules just described.  If your data structure is not deep enough for the requested operation, a compile time error is raised.

See `example-data-1d.cc` and `example-data-2d.cc` for plenty of basic and exotic examples.

# Container and tuple datatypes

The following array-like containers are supported:

* STL containers (e.g. `std::vector`), as well as anything with a similar iterator interface.
* C style arrays (e.g. `double[10]`), but try to avoid them!  You should use `boost::array` or `std::array` instead.
* Blitz++
* Armadillo
* It is relatively easy to add support for other types, so if you need something just let me know.

Containers can be nested (e.g. `std::vector<std::vector<double>>`).  Note, however that certain types of containers such as `blitz::Array` should never be put inside an STL container!  The reason is that the assignment operator for `blitz::Array` doesn't work the way STL expects and this leads to memory corruption and crashes.

The following tuple datatypes are supported:

* `std::pair`
* `boost::tuple`
* `boost::tie` (like `boost::tuple` but uses a reference rather than a copy)
* `std::tuple`
* `std::tie` (like `std::tuple` but uses a reference rather than a copy)
* `blitz::TinyVector`

Tuples and containers can be nested arbitrarily.  For example, you can have a `std::pair` of `std::vector` of `std::pair`.  Tuples correspond to columns.

Support for 3rd party libraries like Blitz++ and Armadillo is enabled by including `gnuplot-iostream.h` *after* the 3rd party library headers.  You are also allowed to include `gnuplot-iostream.h`, then the 3rd party headers, then `gnuplot-iostream.h` again:

	#include "gnuplot-iostream.h"

	// lots of stuff here...

	#include <blitz/array.h>
	// now load in support for Blitz++
	#include "gnuplot-iostream.h"

# 1d vs. 2d data

All of the data sending commands come in 1d and 2d variants (e.g. `send1d()` and `send2d()`).  1d data is for points and curves.  2d data is for images and surfaces.

If you send a 3x2 dimensional `std::vector<std::vector<double> >` to `send1d()` it will send something that looks like
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

# `colmajor` option

You can store an array of columns in a `std::vector<std::vector<double> >`.  Logically, considering the C convention for ordering indices, the last index should correspond to the column.  So if the array contained the data `[[1,2],[3,4],[5,6]]` then `send1d()` would send
	1 2
	3 4
	5 6
However, considering that the number of columns is typically much smaller than the number of rows, it is more efficient when using a `std::vector<std::vector<double> >` to have the first index correspond to the column.  If you store your data that way then use `send1d_colmajor()`.  The `[[1,2],[3,4],[5,6]]` data would then be sent as
	1 3 5
	2 4 6

There is a `send2d_colmajor()` for multiple column gridded data.  The binary senders also have `*_colmajor()` variants.  There are examples of both 1d and 2d colmajor commands in `example-data-1d.cc` and `example-data-2d.cc`.

There is no need to use colmajor for things like `std::pair<std::vector<double>, std::vector<double>>`.  Anything tuple-like (`std::pair`, `std::tuple`, `boost::tuple`, `blitz::TinyVector`) is always associated with columns.  The colmajor option applies to the outermost non-tuple container.

# Binary data: array vs. record

FIXME - to be written

# Custom datatypes

If you define your own custom data type (e.g. a `struct`) then you must tell gnuplot-iostream how to print it.  This is done by providing a specialization of the `TextSender` class (for methods like `send1d()` that send text) or the `BinfmtSender` and `BinarySender` classes (for methods like `sendBinary1d()` that send binary data).  For an example, take a look at `example-data-1d.cc`.

If there is no specialization of `TextSender` then the `<<` operator is used.  So you can also just provide a specialization of `operator<<(std::ostream &, ...)`.

# Custom array types

STL containers, Armadillo, and Blitz++ are supported.  Support for other containers is added by providing a specialization of the `ArrayTraits` class.  For an example, look at the Armadillo and Blitz++ portions of `gnuplot-iostream.h`.
