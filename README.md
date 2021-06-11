

# ORCA36 demonstrator

![plot](https://github.com/immerse-project/ORCA36-demonstrator/blob/main/figs/socurloverf_ORCA36-T404_ALL_2014-06-30_00_RdBu05.png)<br>


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

example of script to launche the model        : SCRIPT/NEMO.sub

ORCA36 Namelists                              : NAMLST

XIOS files                                    : XML
~                                                  
