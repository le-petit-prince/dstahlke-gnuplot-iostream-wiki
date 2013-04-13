# Overview

FIXME - to be written

First one or two (for send1d or send2d) axes label correspond to line (for send1d) or block,line (for send2d).  Remaining axes correspond to columns.  If `colmajor` is used, then first axis is used for columns.

See `example-data-1d.cc` and `example-data-2d.cc`.

# 1d vs. 2d data

All of the data sending commands come in 1d and 2d variants (e.g. `send1d()` and `send2d()`).  1d data is for points and curves (basically anything you would use the `plot` command for).  2d data is for images and surfaces (basically anything sent to `splot`).

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

# Container datatypes

The following array-like containers are supported:

* STL containers (e.g. `std::vector`), as well as anything with a similar iterator interface.
* Blitz++
* Armadillo

Containers can be nested (e.g. `std::vector<std::vector<double>>`).  Note, however that certain types of containers such as `blitz::Array` should never be put inside an STL container!  The reason is that the assignment operator for `blitz::Array` doesn't work the way STL expects and this leads to memory corruption and crashes.

The following tuple datatypes are supported:

* `std::pair`
* `boost::tuple`
* `boost::tie` (like `boost::tuple` but uses a reference rather than a copy)
* `std::tuple`
* `std::tie` (like `std::tuple` but uses a reference rather than a copy)
* `blitz::TinyVector`

Tuples and containers can be nested arbitrarily.  For example, you can have a `std::pair` of `std::vector` of `std::pair`.  Tuples correspond to columns.

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

# Custom datatypes

If you define your own custom data type (e.g. a `struct`) then you must tell gnuplot-iostream how to print it.  This is done by providing a specialization of the `TextSender` class (for methods like `send1d()` that send text) or the `BinfmtSender` and `BinarySender` classes (for methods like `sendBinary1d()` that send binary data).  For an example, take a look at `example-data-1d.cc`.

If there is no specialization of `TextSender` then the `<<` operator is used.  So you can also just provide a specialization of `operator<<(std::ostream &, ...)`.

# Custom array types

STL containers, Armadillo, and Blitz++ are supported.  Support for other containers is added by providing a specialization of the `ArrayTraits` class.  For an example, look at the Armadillo and Blitz++ portions of `gnuplot-iostream.h`.
