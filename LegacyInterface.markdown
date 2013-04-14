# Legacy interface

The functions `send`, `binfmt`, `sendBinary`, `file`, and `binaryFile` are deprecated.  Don't use them.  Here's the problem: you can send an array of columns of data using a datatype like `std::vector<std::vector<double>>`.  But is it the first index or the second that corresponds to the columns?  And how to know whether it should be an array of columns or a grid of scalars?  In earlier versions of this library only a few datatype were supported and ad-hoc rules were assigned to interpreting each one.  Now a much wider range of datatypes are supported, and this way no longer makes sense.  Now you have to explicitly tell whether the data is 1d (for points/curves) or 2d (gridded data like surfaces), and whether the first index or the last corresponds to the columns.  The old methods still work, and the ad-hoc rules have been extended but these should not be used.

# Upgrade

To upgrade from the legacy interface to the new interface, just use `send1d`, `send2d`,
`send1d_colmajor`, or `send2d_colmajor` in place of `send` (and similarly for the other methods
like `file` and `sendBinary`).  To raise a warning when the deprecated methods are used, define
the preprocessor macro `GNUPLOT_DEPRECATE_WARN`.
