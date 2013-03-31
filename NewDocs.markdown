# Intro

# Basic usage

## Quick example

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
