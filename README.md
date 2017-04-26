WARNING: these instructions are still being drafted
===================================================


Instructions
============

In case you want to perform the tutorial using your own laptop please follow closely these instructions.

You will have internet access, so if you prefer you can run the tutorial on a remote machine at your university. Make sure that you are able to connect to this machine from outside your university network. Also consider that visualization of trajectories using VMD might be very slow across as `ssh` connection. Finally, notice that installing some of the libraries below requires `sudo` (root access on your machine). If necessary, ask your system administrator to install the libraries for you.

Requirements
------------

For the tutorial you will beed the following software to be installed:

- C++ compiler (gcc, clang, or intel).
- Git.
- Cmake.
- Gawk.
- Libmatheval.
- MPI library (either openmpi or mpich).
- Gnuplot to plot data files.
- VMD to visualize the trajectories.
- PLUMED from master branch of github repository, updated when the tutorial starts
- GROMACS 5.1 patched with PLUMED using `patch -p --runtime`, so that PLUMED can be updated easily.

If you already have all this stuff ready you can stop here. Otherwise follow the steps below.

Notice that if you have an older version of PLUMED installed (e.g. 2.3.x) it will not be sufficient for some of the exercizes.

Supported operating systems
---------------------------

Instructions below are for Linux and OSX. With OSX we will assume you have Developer Tools as well as either [MacPorts](http://www.macports.org) or [HomeBrew](http://brew.sh/) installed. In case you don't, choose one among them and install it. Windows is not supported.

Install libraries & other stuff (Linux)
---------------------------------------

Install git, cmake, gawk, libmatheval, MPI, and gnuplot:
    
    sudo apt-get install git cmake gawk
    sudo apt-get install libmatheval-dev
    sudo apt-get install libopenmpi-dev openmpi-bin
    sudo apt-get install gnuplot

You can download VMD binaries [here](http://www.ks.uiuc.edu/Development/Download/download.cgi?PackageName=VMD).

__TODO: add instructions for python__

Install libraries & other stuff (OSX with MacPorts)
---------------------------------------------------

Install git, cmake, gawk, libmatheval, MPI, and gnuplot:

    sudo port install git cmake gawk
    sudo port install libmatheval
    sudo port install openmpi
    sudo port install gnuplot

You can download VMD binaries [here](http://www.ks.uiuc.edu/Development/Download/download.cgi?PackageName=VMD).

__TODO: add instructions for python__

Install libraries & other stuff (OSX with HomeBrew)
---------------------------------------------------

__TODO: add instructions for libmatheval__

Install git, cmake, gawk, libmatheval, MPI, and gnuplot:

    sudo brew install git cmake gawk
    sudo brew install openmpi
    sudo brew install gnuplot

You can download VMD binaries [here](http://www.ks.uiuc.edu/Development/Download/download.cgi?PackageName=VMD).

__TODO: add instructions for python__

Install PLUMED
--------------

Extensive installation instructions for PLUMED can be found [here](https://plumed.github.io/doc-master/user-doc/html/_installation.html).

Configure PLUMED:

    git clone https://github.com/plumed/plumed2.git
    cd plumed2
    ./configure
    
After command `./configure` please check carefully the reported warnings. If either `matheval` or `MPI` were not found you should check the previous steps. If you are using MacPorts and `MPI` was not detected, it might be that you have to do

    ./configure CXX=mpicxx-openmpi-mp

After you succeeded in configuring, you should compile PLUMED:

    make -j 4
    source sourceme.sh

While compiling PLUMED you might receive an error like this one:

    relocation R_X86_64_32 against `MPIR_ThreadInfo' can not be used when making a shared object; recompile with -fPIC

This means that you should reinstall your MPI library as a shared library.

Now you should be able to run `plumed` from the command line. Try

    plumed --help

You should see something like this

    Usage: plumed [options] [command] [command options]
      plumed [command] -h|--help: to print help for a specific command
    Options:
      [help|-h|--help]          : to print this help
      [--is-installed]          : fails if plumed is not installed
    # etcetera #

As a double check try to execute the following commands

    plumed --has-mpi && echo ok
    plumed --has-matheval && echo ok

In both cases the result should be `ok`.

Everytime you want to use PLUMED you should first run the command

    source /path/to/plumed2/sourceme.sh

Here `/path/to/plumed2` is the directory where you compiled PLUMED.

Install GROMACS
---------------

Before that PLUMED is installed and functional before continuing with this step. Download GROMACS and patch it with PLUMED
    
    wget ftp://ftp.gromacs.org/pub/gromacs/gromacs-5.1.4.tar.gz
    tar xzf gromacs-5.1.4.tar.gz
    cd gromacs-5.1.4
    mkdir build
    cd build
    plumed patch -p --runtime -e gromacs-5.1.4

For the last command you need `plumed` executable to be in your path. Be sure that you do not get any warning.

Configure GROMACS:

    cmake \
     -DCMAKE_C_COMPILER=mpicc \
     -DCMAKE_CXX_COMPILER=mpicxx \
     -DCMAKE_INSTALL_PREFIX=$HOME/opt/ \
     -DGMX_THREAD_MPI:BOOL=OFF \
     -DGMX_MPI:BOOL=ON  ../

Note on OSX with MacPorts: in case the name of the MPI compiler is `mpicxx-openmpi-mp`, in the above commands you should replace `mpicc` with `mpicc-openmpi-mp` and `mpicxx` with `mpicxx-openmpi-mp`.

Compile GROMACS:

    make -j 4
    
Now you should be able to run GROMACS using the following command

    gmx_mpi mdrun

Verify if GROMACS was correctly patched by PLUMED. Type

    gmx_mpi mdrun -h | grep plumed

You should see something like this:

          [-plumed [<.dat>]] [-membed [<.dat>]] [-mp [<.top>]] [-mn [<.ndx>]]
     -plumed [<.dat>]           (plumed.dat)     (Opt.)






