FIXME - to be written

# Constructors

	explicit Gnuplot(const std::string &cmd = "gnuplot")
	explicit Gnuplot(FILE *fh)

# Methods for legacy interface

	Gnuplot &send(const T &arg)
	Gnuplot &sendBinary(const T &arg)
	std::string binfmt(const T &arg, const std::string &arr_or_rec="array")
	std::string file(const T &arg, const std::string &filename="")
	std::string binaryFile(const T &arg, const std::string &filename="", const std::string &arr_or_rec="array")

# Methods for sending data in text format to gnuplot's stdin

	template <typename T> Gnuplot &send1d         (const T &arg)
	template <typename T> Gnuplot &send2d         (const T &arg)
	template <typename T> Gnuplot &send1d_colmajor(const T &arg)
	template <typename T> Gnuplot &send2d_colmajor(const T &arg)

# Methods for sending data in binary format to gnuplot's stdin

	template <typename T> std::string binFmt1d         (const T &arg, const std::string &arr_or_rec)
	template <typename T> std::string binFmt2d         (const T &arg, const std::string &arr_or_rec)
	template <typename T> std::string binFmt1d_colmajor(const T &arg, const std::string &arr_or_rec)
	template <typename T> std::string binFmt2d_colmajor(const T &arg, const std::string &arr_or_rec)

	template <typename T> Gnuplot &sendBinary1d         (const T &arg)
	template <typename T> Gnuplot &sendBinary2d         (const T &arg)
	template <typename T> Gnuplot &sendBinary1d_colmajor(const T &arg)
	template <typename T> Gnuplot &sendBinary2d_colmajor(const T &arg)

# Methods for sending text data via temporary or permanent file

	template <typename T> std::string file1d         (const T &arg, const std::string &filename="")
	template <typename T> std::string file2d         (const T &arg, const std::string &filename="")
	template <typename T> std::string file1d_colmajor(const T &arg, const std::string &filename="")
	template <typename T> std::string file2d_colmajor(const T &arg, const std::string &filename="")

# Methods for sending binary data via temporary or permanent file

	template <typename T> std::string binFile1d         (const T &arg, const std::string &arr_or_rec, const std::string &filename="")
	template <typename T> std::string binFile2d         (const T &arg, const std::string &arr_or_rec, const std::string &filename="")
	template <typename T> std::string binFile1d_colmajor(const T &arg, const std::string &arr_or_rec, const std::string &filename="")
	template <typename T> std::string binFile2d_colmajor(const T &arg, const std::string &arr_or_rec, const std::string &filename="")

# Other methods

	void clearTmpfiles()

	void getMouse(double &mx, double &my, int &mb, std::string msg="Click Mouse!")
