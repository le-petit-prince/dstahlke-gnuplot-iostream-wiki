# Intro

This interface allows gnuplot to be controlled from C++ and is designed to be the lowest hanging fruit.  In other words, if you know how gnuplot works it should only take 30 seconds to learn the basic usage.  On the other hand, it is also very versatile.  Basically it is just an iostream pipe to gnuplot with some extra functions for pushing data arrays and getting mouse clicks.  This is a low level interface, and usage involves manually sending commands to gnuplot using the `<<` operator (so you need to know gnuplot syntax).  This is in my opinion the easiest way to do it if you are already comfortable with using gnuplot.  If you would like a more high level interface check out the [gnuplot-cpp library](http://code.google.com/p/gnuplot-cpp).

Supported data sources include STL containers (eg.  vector), Blitz++, and armadillo.  You can use nested data types like `std::vector<std::vector<std::pair<double, double>>>` (as well as even more exotic types).  Support for custom data types is possible.

If you have any questions, bugs, or suggestions, email me at `dan@stahlke.org`.

Table of contents:

*  [Basic Usage](BasicUsage)
*  [Datatypes](Datatypes)
*  [Stdin vs. temporary files](StdinVsFiles)
*  [Binary data](BinaryData)
*  [Legacy interface](LegacyInterface)
*  [Capturing mouse events](Mouse)
*  [Reference](Reference)
*  [Portability](Portability)
*  [FAQ](FAQ)
