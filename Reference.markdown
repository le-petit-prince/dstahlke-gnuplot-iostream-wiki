FIXME - to be written

tell about namespace

# Gnuplot class

## Constructors

	explicit Gnuplot(const std::string &cmd = "gnuplot")
	explicit Gnuplot(FILE *fh)

## Methods for legacy interface

	Gnuplot &send(const T &arg)
	Gnuplot &sendBinary(const T &arg)
	std::string binfmt(const T &arg, const std::string &arr_or_rec="array")
	std::string file(const T &arg, const std::string &filename="")
	std::string binaryFile(const T &arg, const std::string &filename="", const std::string &arr_or_rec="array")

## Methods for sending data in text format to gnuplot's stdin

	Gnuplot &send1d         (const T &arg)
	Gnuplot &send2d         (const T &arg)
	Gnuplot &send1d_colmajor(const T &arg)
	Gnuplot &send2d_colmajor(const T &arg)

## Methods for sending data in binary format to gnuplot's stdin

	std::string binFmt1d         (const T &arg, const std::string &arr_or_rec)
	std::string binFmt2d         (const T &arg, const std::string &arr_or_rec)
	std::string binFmt1d_colmajor(const T &arg, const std::string &arr_or_rec)
	std::string binFmt2d_colmajor(const T &arg, const std::string &arr_or_rec)

	Gnuplot &sendBinary1d         (const T &arg)
	Gnuplot &sendBinary2d         (const T &arg)
	Gnuplot &sendBinary1d_colmajor(const T &arg)
	Gnuplot &sendBinary2d_colmajor(const T &arg)

## Methods for sending text data via temporary or permanent file

	std::string file1d         (const T &arg, const std::string &filename="")
	std::string file2d         (const T &arg, const std::string &filename="")
	std::string file1d_colmajor(const T &arg, const std::string &filename="")
	std::string file2d_colmajor(const T &arg, const std::string &filename="")

## Methods for sending binary data via temporary or permanent file

	std::string binFile1d         (const T &arg, const std::string &arr_or_rec, const std::string &filename="")
	std::string binFile2d         (const T &arg, const std::string &arr_or_rec, const std::string &filename="")
	std::string binFile1d_colmajor(const T &arg, const std::string &arr_or_rec, const std::string &filename="")
	std::string binFile2d_colmajor(const T &arg, const std::string &arr_or_rec, const std::string &filename="")

## Other methods

	void clearTmpfiles()

	void getMouse(double &mx, double &my, int &mb, std::string msg="Click Mouse!")

# Classes for custom datatypes

	TextSender
	BinfmtSender
	BinarySender

# Class for custom container types

	ArrayTraits
