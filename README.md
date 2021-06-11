

# ORCA36 demonstrator

This page describe the way to build a global 1/36° global configuration (ORCA36) based on  [NEMO OGCM](https://www.nemo-ocean.eu/).

This bench represents the milestone MS31 "Configuration available for final HPC tests in WP4" for IMMERSE project.


![plot](https://github.com/immerse-project/ORCA36-demonstrator/blob/main/figs/socurloverf_ORCA36-T404_ALL_2014-06-30_01_RdBu_low.png)<br>


## Installation

- Download XIOS:

```svn co -r 2130 http://forge.ipsl.jussieu.fr/ioserver/svn/XIOS/trunk XIOS ```

- Downlaod NEMO:

```svn co -r 14365 https://forge.ipsl.jussieu.fr/nemo/svn/NEMO/trunk trunk_r14365 ```


## Compiling:

example of module list used on Mare Nostrum IV: ``` intel/2018.4 impi/2018.4 hdf5/1.8.19 pnetcdf/1.8.1 netcdf/4.4.1.1 ```

create the configuration                      : ``` ./makenemo -m your_archfile -r ORCA2_ICE_PISCES -n ORCA36 -j 20 ```

change ORCA36 cpp keys by                     : ```  key_si3 key_xios key_qco ```

and recompile                                 : ``` ./makenemo -m your_archfile -r ORCA36 -j 20 ```

## Execution

example of script to launche the model        : [script](SCRIPT/NEMO.sub)

ORCA36 Namelists                              : [namelist](NAMLST/)

XIOS files                                    : [XIOS files](XML/)

Configuration files: ``` ftp://ftp.mercator-ocean.fr/download/users/cbricaud/BENCH-ORCA36-INPUT.tar.gz ```

Forcing files      : ``` ftp://ftp.mercator-ocean.fr/download/users/cbricaud/BENCH-ORCA36-INPUT.tar.gz ```


