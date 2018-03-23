# lrose-examples
parameter files, auxiliary files, notebooks, and data files for LROSE examples

Examples 1 & 2 are useful for lrose-blaze version of the toolkit.

## Scenarios 

### Use RadxCheck to find where problems occurred using RadxConvert

### Use RadxCheck to verify a home grown CfRadial has correct format
If data file is airborne radar file or tail radar file, is it formatted correctly?  Does it have the necessary parameters, and fields?

### When I run RadxConvert, what level of debug is best?  -debug, -verbose, or -very_verbose?

### What is the first step? Run RadxPrint on data file to first characterize the data format.  
  See if the LROSE tools can read the data files.  

### Display the data in native coordinates using HawkEye.
NOTE: HawkEye assumes directory naming by date (yyyymmdd) and file naming using data and time formats.  Following the naming
convention, HawkEye can quickly scan through and display the data.

```
$ ./HawkEye -f <data-file>
```

For one directory full of files; each file contains one radar volume ...
```
$ ./HawkEye -f <directory>
$ ./HawkEye -f <list of files>
```

### What is the naming convention for directories and data files?  
Follow the workflow and all the naming conventions are automatically generated.

### What if the date is NOT in the directory or file name?
Run the data files through RadxConvert to generate the data file organization expected by HawkEye.
One sweep in each file
One file = one volume

###  I have Dorade data file.  How can I use the LROSE tools?   
Use RadxConvert -aggregate to generate CfRadial files containing one volume with multiple sweeps.

