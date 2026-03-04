# opsis
## python command line output

Note: This is an experimental package, the API is not stable.

A very simple package to make nice output for CLI applications.
`opsis` is particularly useful for small applications that perform a number of discrete tasks, for instance parsing large output files. When doing that, `opsis` lets you time intermediate steps, show a spinner or a progress counter, and print intermediate output and warnings without disrupting such animations.

Usage: import `opsis.Comms` in the top level of your own package. Then you use an instance of `Comms`, configured at will, as a general printing interface.
