# FRE-NCtools

FRE-NCtools is a collection of tools to help with the creation and
manipulation of netCDF files used or written by the climate
models developed at the
[Geophysical Fluid Dynamics Laboratory](https://www.gfdl.noaa.gov)
(GFDL).  These tools were primarily written by members of the GFDL
[Modeling Systems Group](https://www.gfdl.noaa.gov/modeling-systems)
to be used in the
[Flexible Modeling System](https://www.gfdl.noaa.gov/fms) (FMS)
[Runtime Environment](https://www.gfdl.noaa.gov/fre) (FRE).

[![Actions](https://github.com/NOAA-GFDL/FRE-NCtools/workflows/FRE-NCtools%20CI/badge.svg)](https://github.com/NOAA-GFDL/FRE-NCtools/actions)
[![License: LGPL v3](https://img.shields.io/badge/License-LGPL%20v3-blue.svg)](https://www.gnu.org/licenses/lgpl-3.0)

## Tools

The tools available in FRE-NCtools are:

### Combine Tools

* combine_restarts -- Combine restart files generated by a FMS enabled model
* iceberg_comb -- Combine iceberg history files
* combine-ncc -- Combine distributed unstructured FMS grid netCDF files
* decompress-ncc -- Convert an unstructured FMS grid file to a standard lat-lon grid
* mppnccombine -- Combine destributed FMS netCDF files
* is-compressed -- Determine if a netCDF file has an unstructured FMS grid
* scatter-ncc -- Distribute an unstructured FMS grid netCDF file for initializing a FMS climate model
* mppncscatter -- Distribute FMS netCDF file for initializing a FMS climate model

### Statistical and Informational Tools

* list_ncvars -- List the variables in a netCDF file
* plevel -- Interpolates data from model levels to pressure levels
* split_ncvars -- Write the variables in a FMS netCDF file into multiple netCDF files, one file per netCDf field
* timavg -- Create a time average netCDF file
* ncexists -- Checks for variables and attributes in a netCDF file
* nc_null_check -- Checks to see if the value of the attribute *bounds* of variable *lat* has a null character

### Grid Tools
* check_mask -- Configure the processors which contains all land points to be masked out for ocean model
* fregrid -- Convert from one grid resolution to another
* make_coupler_mosaic -- Generates three exchange grids for the FMS coupler
* make_hgrid -- Generate different types of horizontal grids
* make_land_domain -- Generate a land domain file
* make_quick_mosaic -- Generate a complete grid file for the FMS coupler
* make_regional_mosaic -- Generate horizontal grid and solo mosaic to regrid regional output onto a regular lat-lon grid
* make_solo_mosaic -- Generates Mosaic information between tiles
* make_topog -- Generate the topography for any Mosaic
* make_vgrid -- Generate a vertical grid for a FMS model
* remap_land -- Remap land restart file from one mosaic grid to another mosaic grid
* river_regrid -- Remap river network data from global regular lat-lon grid onto any other grid
* runoff_regrid -- Regrid land runoff data to the nearest ocean point
* transfer_to_mosaic_grid -- Convert older style grids to newer mosaic grid

## Cloning and submodules
The NCTools github repository contains the GFDL ocean_model_grid_generator repository
as a submodule. After cloning NCTools, it must be initialized and updated for its
submodules:

```
git clone --recursive https://github.com/NOAA-GFDL/FRE-NCtools
cd FRE-NCtools
git submodule update --init --recursive
```

## Building and Installation - General
FRE-NCtools has a collection of C and Fortran sources. Within GFDL, FRE-NCtools
is built using a recent version of the GNU and Intel C and Fortran compilers.
Compiler flags are required for compilation and they are commonly set in
environment varialbes. The ocean_model_grid_generator is a Python3 project,
and its installation may require the use of configutration options.

Some of the commonly needed environment variables are:
  CC          C compiler command
  FC          Fortran compiler command
  CFLAGS      C compiler flags
  FCFLAGS     Fortran compiler flags
  LDFLAGS     linker flags, e.g. -L<lib dir> if you have libraries in a
              nonstandard directory <lib dir>

On many systems, information of the NetCDF headers and libraries
can be obtained by the nf-config and nc-config commands.

## Building and Installation - possible minimum configuration

If you received this as a package, the standard:

```
cd FRE-NCTools
configure
make
make install
```

should be sufficient.  If you cloned the git repository, you must first run
the `autoreconf` command with the `--install` option to install missing
autotools files:

```
cd FRE-NCTools
autoreconf -i
./configure
make
make install
```

## Building and Installation - Common options
The legal options to the `make` and the `configure` commands can be listed by the commands:
```
make --help
configure --help=recursive
```

It is common to compile into a build directory (say build_dir) and
install into an installation directory (say install_dir). If the
ocean_model_grid_generator is desired, it may be convenient to allow
the build system to set up a Python venv. These three choices can be
done with these steps:
```
cd FRE-NCTools
autoreconf -i
mkdir build_dir && cd build_dir
../configure --prefix=install_dir --enable-venv
make
make install
```

If the ocean_model_grid_generator is not desired, a similar configuration would
be achieved by :

```
cd FRE-NCTools
autoreconf -i
mkdir build_dir && cd build_dir
../configure --prefix=install_dir --disable-ocean-model-grid-generator
make
make install
```

## Building on a GFDL-managed system
The recommended environment with the recommended autoconf defaults can be loaded by running
the output generated by the site-specific `env.sh` script. The script are run before running
the autoreconf command, and  they are located in the [site-configs](site-configs) directory.
For example:

```
# Note: env.sh builds a set of commands to configure the environment.
# The backquotes (not single quotes!) do command substitution, using the
# standard output of env.sh as input to "eval". eval then executes the
# set of environment-setting shell commands.
eval `site-configs/<site>/env.sh`
```

## Building parallel NCTools applications
To also build the parallel running version of certain FRE-NCtools applications,
the `configure` command should be given the option `--with-mpi`.

## Compiler Requirements.

A compiler with support for the C99 standard is required since NCTools release 18.1.
Compile time errors may be generated by compilers that do not support this standard,
and upgrading to a sufficiently recent compiler version is recommended in this case.
NCTools has been built and tested with GCC and Intel compilers.

## Documentation

An embarrassingly small number of the commands have man pages.  These man pages
are written using [AsciiDoc](http://asciidoc.org/) format.  If AsciiDoc is found
on the system, the man pages will be built and installed automatically.

Most commands support either a `-h` or `--help` options.  In some cases, this
should be enough to get you started using the commands.  We are in the process
of collecting more information on the tools available in this package.  These
will be added when available.

Commonly, to combine restart and history files, one should run is-compressed on
the candidate file first.

If it returns 0, it's land compressed file that needs:
1. combine-ncc inputfile1 inputfile2 outputfile
2. decompress-ncc inputfile outputfile

If is-compressed returns non-zero, it's a regular distributed file that gets:
1. mppnccombine outputfile inputfile1 inputfile2 ...


## Disclaimer

The United States Department of Commerce (DOC) GitHub project code is provided
on an 'as is' basis and the user assumes responsibility for its use.  The DOC has
relinquished control of the information and no longer has responsibility to
protect the integrity, confidentiality, or availability of the information.  Any
claims against the Department of Commerce stemming from the use of its GitHub
project will be governed by all applicable Federal law. Any reference to
specific commercial products, processes, or services by service mark,
trademark, manufacturer, or otherwise, does not constitute or imply their
endorsement, recommendation or favoring by the Department of Commerce.  The
Department of Commerce seal and logo, or the seal and logo of a DOC bureau,
shall not be used in any manner to imply endorsement of any commercial product
or activity by DOC or the United States Government.

This project code is made available through GitHub but is managed by NOAA-GFDL
at https://www.gfdl.noaa.gov.
