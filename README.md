WARNING: these instructions are still being updated
===================================================


Instructions
============

If you have a smart phone please install Socrative Student from the App/Play store prior to the meeting.  You can find this app by searching
for Socrative in the App/Play store.  Make sure that you install the student app and not the teacher app.

Using SISSA workstations
========================
If you want to perform the tutorials using SISSA workstations you should just use this command

    module load plumed-tutorial-2017
    
This command will load the proper version of plumed and gromacs, plus python/gnuplot/etc.
You can verify which modules were loaded by typing `module list`, that should result in something like this:

    Currently Loaded Modulefiles:
    1) intel/15.0                 4) python/3.3.2               7) vmd/1.9.2
    2) openmpi/1.8.5/intel/15.0   5) numpy/1.11.1/python/3.3    8) Gawk/4.1.4
    3) gnuplot/5.0.4              6) scipy/0.17.1/python/3.3    9) plumed-tutorial-2017/1.0


Using your laptop
=================
If you want to perform the tutorials using your own laptop please follow closely these instructions.

You will have internet access, so if you prefer you can run the tutorial on a remote machine at your university. Make sure that you are able to connect to this machine from outside your university network. Also consider that visualization of trajectories using VMD might be very slow across as `ssh` connection. Finally, notice that installing some of the libraries below requires `sudo` (root access on your machine). If necessary, ask your system administrator to install the libraries for you.

If you have any question or feedback, please write to the plumed mailing list `plumed-users@googlegroups.com` so that everyone can benefit of questions and answers. In addition, if you are familiar with github and you want to improve this document, feel free to [open a pull request](https://github.com/plumed/tuto-trieste-instructions/pulls).

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

In addition, if you have a smartphone or a tablet, please install [Socrative student](https://www.socrative.com/apps.html)
in advance.

If you already have all this stuff ready you can stop here. Otherwise follow the steps below.

Notice that if you have an older version of PLUMED installed (e.g. 2.3.x) it will not be sufficient for some of the exercizes.

Supported operating systems
---------------------------

Instructions below are for Linux and OSX. With OSX we will assume you have Developer Tools as well as either [MacPorts](http://www.macports.org) or [HomeBrew](http://brew.sh/) installed. In case you don't, choose one among them and install it. Windows is not supported. In addition we are trying to setup a [Docker](http://www.docker.com) image that should work across multiple operating systems, more at the end of this page.

GNU AWK
-------

We will use GNU AWK in some exercize. Be sure your locale is set correctly. Please try

     gawk 'BEGIN{print 3/2}'
     
If you see `1.5` that's ok. If you see `1,5` (notice the comma!) then you have to change your locale settings. Usually adding `export LANG=C` to your bash configuration file is enough.

Install libraries & other stuff (Linux)
---------------------------------------

Install git, cmake, gawk, libmatheval, MPI, and gnuplot:
    
    sudo apt-get install git cmake gawk
    sudo apt-get install libmatheval-dev
    sudo apt-get install libopenmpi-dev openmpi-bin
    sudo apt-get install gnuplot
    sudo apt-get install python3 python3-numpy python3-scipy

You can download VMD binaries [here](http://www.ks.uiuc.edu/Development/Download/download.cgi?PackageName=VMD).

Install libraries & other stuff (OSX with MacPorts)
---------------------------------------------------

Install git, cmake, gawk, libmatheval, MPI, and gnuplot:

    sudo port install git cmake gawk
    sudo port install libmatheval
    sudo port install openmpi
    sudo port install gnuplot
    sudo port install python36 py36-numpy py36-scipy

You can download VMD binaries [here](http://www.ks.uiuc.edu/Development/Download/download.cgi?PackageName=VMD).

Make sure that your compiler is able to see the libraries you installed. Add to your `.bashrc` file the following lines

    export PATH="/opt/local/bin:$PATH"
    export CPATH="/opt/local/include:$CPATH"
    export INCLUDE="/opt/local/include:$INCLUDE"
    export LIBRARY_PATH="/opt/local/lib:$LIBRARY_PATH"
    export LD_LIBRARY_PATH="/opt/local/lib:$LD_LIBRARY_PATH"

Install libraries & other stuff (OSX with HomeBrew)
---------------------------------------------------

__TODO: add instructions for libmatheval__

Install git, cmake, gawk, libmatheval, MPI, and gnuplot:

    sudo brew install git cmake gawk
    sudo brew install openmpi
    sudo brew install gnuplot
    sudo brew install python3
    sudo brew install numpy --with-python3
    sudo brew install scipy --with-python3

You can download VMD binaries [here](http://www.ks.uiuc.edu/Development/Download/download.cgi?PackageName=VMD).

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

Make sure that PLUMED is installed and functional before continuing with this step. Download GROMACS and patch it with PLUMED
    
    wget ftp://ftp.gromacs.org/pub/gromacs/gromacs-5.1.4.tar.gz
    tar xzf gromacs-5.1.4.tar.gz
    cd gromacs-5.1.4
    plumed patch -p --runtime -e gromacs-5.1.4
    mkdir build
    cd build

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
    make install
    
Then add `$HOME/opt/bin` to your path. For instance, you can add this command to your `.bashrc` file: `PATH="$HOME/opt/bin:$PATH`. Now you should be able to run GROMACS using the following command

    gmx_mpi mdrun

Verify if GROMACS was correctly patched by PLUMED. Type

    gmx_mpi mdrun -h | grep plumed

You should see something like this:

          [-plumed [<.dat>]] [-membed [<.dat>]] [-mp [<.top>]] [-mn [<.ndx>]]
     -plumed [<.dat>]           (plumed.dat)     (Opt.)


Docker image
------------

Make sure Docker is installed and running on your computer. You can run an image containing PLUMED, GROMACS, and the other required tools using this command

    docker run -it rinnocente/gromed-ts-tutorial-2017 /bin/bash

In case you want to share files between your machine and the Docker container, you can use the following command instead

    docker run -v "$(pwd)":/home/gromed/data -it rinnocente/gromed-ts-tutorial-2017 /bin/bash

The working directory in your laptop will be then accessible as `$HOME/data` inside the Docker container.

More detailed instructions can be found at [this link](https://github.com/rinnocente/gromed-ts-tutorial-2017).

Many thanks to Roberto Innocente (SISSA) for setting this up!





