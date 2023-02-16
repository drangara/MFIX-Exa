Joule 2.0
==========

Basics
-------

Access
>>>>>>>

You need an NETL HPC account, yubikey and the `HPC client <https://hpc.netl.doe.gov/support/hpc-client/index.html>`_


Running Jobs
>>>>>>>>>>>>

Common Slurm commands:

* **sinfo** see available/allocated resources
* **sbatch runit_cpu.sh** submit a cpu job to the queue
* **squeue -u USER** check job status of user USER
* **squeue -p PARTITION** check job status of partition PARTITION
* **scancel JOBID** kill a job with id JOBID
* **salloc -N 1 -p gpu** grab a GPU node interactively (for up to 48 hrs) 
* **salloc -N 2 -p dev -q dev** grab two development nodes (for up to 2 hrs)


Building MFIX-Exa: cmake
-------------------------

Standalone Build
>>>>>>>>>>>>>>>>>

#. Clone
   
   For the basic, standalone CPU and GPU builds, first clone the code, 
   checkout the desired branch, update the submodules and create a build directory

   .. code:: bash

    git clone https://mfix.netl.doe.gov/gitlab/exa/mfix.git
    cd mfix
    git checkout develop
    git submodule update --init
    mkdir build && cd build/

#. Build mfix
   
   For a CPU load the necessary modules and build from the head node. For GPU first connect to an interactive node and then build:
   
   .. tabs::
      
      .. tab:: CPU

         .. code:: bash

            ## load the necessary modules
            module purge
            module load vgl
            module load grace
            module load cmake/3.23.1
            module load gnu/9.3.0
            module load openmpi/4.0.4_gnu9.3

            ## build mfix
            cmake -DCMAKE_C_COMPILER=gcc -DCMAKE_CXX_COMPILER=g++ -DCMAKE_Fortran_COMPILER=gfortran -DMFIX_MPI=yes -DMFIX_OMP=no -DMFIX_GPU_BACKEND=NONE -DAMReX_TINY_PROFILE=no -DMFIX_CSG=no -DMFIX_HYPRE=no -DCMAKE_BUILD_TYPE=Release ../mfix
            make -j 8

            ## uncomment to build the pic2dem restarter app
            #cmake -j8 --build . --target pic2dem

      .. tab:: GPU

         .. code:: bash
            
            ## connect to an interactive node
            salloc -N 1 -p gpu

            ## load Modules
            module purge
            module load vgl
            module load grace
            module load cmake/3.23.1
            module load cuda/11.3
            module load gnu/9.3.0
            module load openmpi/4.0.4_gnu9.3

            ## build mfix
            cmake -DCMAKE_C_COMPILER=gcc -DCMAKE_CXX_COMPILER=g++ -DCMAKE_Fortran_COMPILER=gfortran -DMFIX_MPI=yes -DMFIX_OMP=no -DMFIX_GPU_BACKEND=CUDA -DAMReX_TINY_PROFILE=no -DMFIX_CSG=no -DMFIX_HYPRE=no -DCMAKE_BUILD_TYPE=Release ../mfix  &&  make -j 20

            ## uncomment to build the pic2dem restarter app
            #cmake -j8 --build . --target pic2dem



Full Build
>>>>>>>>>>>

To build MFIX-Exa with hypre, csg and/or ascent dependencies, you first need to build and install these libraries and their dependencies.

Install dependencies
<<<<<<<<<<<<<<<<<<<<<

#. Setup

   .. code:: bash

      ## create directories for installation
      cd ~/packages/
      rm -rf old.mfix-exa_deps/
      mv mfix-exa_deps/ old.mfix-exa_deps/ 
      cd $HOME
      rm -rf tmp.bld-deps/ 
      mkdir tmp.bld-deps && cd tmp.bld-deps/

      ## set and init install directories
      export MY_INSTALL_DIR=$(pwd)/mfix-exa_deps
      export HYPRE_INSTALL_DIR=$MY_INSTALL_DIR/hypre
      export CSG_INSTALL_DIR=$MY_INSTALL_DIR/csg-deps
      export ASCENT_INSTALL_DIR=$MY_INSTALL_DIR/ascent
      mkdir -p $HYPRE_INSTALL_DIR 
      mkdir $CSG_INSTALL_DIR 
      mkdir $ASCENT_INSTALL_DIR 

#. Load modules and set helpers

   For CPU we'll just build on the head node, but for GPU we connect to an interactive node first:

   .. tabs::

      .. tab:: CPU

         .. code:: bash

            module load cmake/3.23.1
            module load gnu/9.3.0
            module load openmpi/4.0.4_gnu9.3
            module load boost/1.77.0_gnu9.3

            export CC=$(which mpicc)
            export CXX=$(which mpic++)
            export F77=$(which mpif77)
            export FC=$(which mpifort)
            export F90=$(which mpif90)

      .. tab:: GPU

         .. code:: bash

            ## connect to an interactive node
            salloc -N 1 -p gpu
            
            ## load modules
            module load cmake/3.23.1
            module load cuda/11.3
            module load gnu/9.3.0
            module load openmpi/4.0.4_gnu9.3
            module load boost/1.77.0_gnu9.3

            export CC=$(which mpicc)
            export CXX=$(which mpic++)
            export F77=$(which mpif77)
            export FC=$(which mpifort)
            export F90=$(which mpif90)


#. HYPRE

   .. tabs::

      .. tab:: CPU

         .. code:: bash

            git clone https://github.com/hypre-space/hypre.git
            pushd hypre/src/
            git checkout v2.26.0
            ./configure --prefix=$HYPRE_INSTALL_DIR --with-MPI
            make -j8 install 
            popd

      .. tab:: GPU

         .. code:: bash

            git clone https://github.com/hypre-space/hypre.git
            pushd hypre/src/
            git checkout v2.26.0
            ./configure --prefix=$HYPRE_INSTALL_DIR --without-superlu --disable-bigint --without-openmp --enable-shared  --with-MPI --with-cuda --with-gpu-arch='60' --with-cuda-home=$CUDA_HOME --enable-cusparse --enable-curand
            make -j32 install 
            popd

#. Catch2

   .. code:: bash

      git clone --depth 1 --branch v2.13.7 https://github.com/catchorg/Catch2
      pushd Catch2/
      cmake -S . -B build -DCMAKE_INSTALL_PREFIX=$CSG_INSTALL_DIR
      cd build/
      make -j8 install
      popd

#. GMP

   .. code:: bash

      wget --no-check-certificate https://ftp.gnu.org/gnu/gmp/gmp-6.2.1.tar.xz
      tar -xf gmp-6.2.1.tar.xz
      pushd gmp-6.2.1
      ./configure --prefix=$CSG_INSTALL_DIR
      make -j8 install
      popd

#. MPFR

   .. code:: bash

      wget --no-check-certificate https://ftp.gnu.org/gnu/mpfr/mpfr-4.1.0.tar.xz
      tar -xf mpfr-4.1.0.tar.xz
      pushd mpfr-4.1.0/
      ./configure --with-gmp=$CSG_INSTALL_DIR --prefix=$CSG_INSTALL_DIR
      make -j8 install
      popd

#. PEGTL

   .. code:: bash

      git clone --branch 3.2.2 https://github.com/taocpp/PEGTL
      pushd PEGTL/
      cmake -S . -B build -DCMAKE_INSTALL_PREFIX=$CSG_INSTALL_DIR
      cd build/
      make -j8 install
      popd

#. Conduit

   .. code:: bash

      git clone --recursive https://github.com/LLNL/conduit.git
      pushd conduit/
      git checkout v0.8.4
      mkdir build && cd build
      cmake -S ../src -DCMAKE_INSTALL_PREFIX=$ASCENT_INSTALL_DIR -DCMAKE_BUILD_TYPE=Release -DENABLE_OPENMP=OFF -DENABLE_MPI=ON -DENABLE_CUDA=OFF 
      make -j8 install
      popd

#. Vtk-m

   .. code:: bash

      git clone --branch master https://gitlab.kitware.com/vtk/vtk-m.git
      pushd vtk-m/
      git checkout v1.9.0
      mkdir build && cd build/
      cmake -S ../ -DCMAKE_INSTALL_PREFIX=$ASCENT_INSTALL_DIR -DCMAKE_BUILD_TYPE=Release -DVTKm_ENABLE_OPENMP=OFF -DVTKm_ENABLE_MPI=ON -DVTKm_ENABLE_CUDA=OFF -DVTKm_USE_64BIT_IDS=OFF -DVTKm_USE_DOUBLE_PRECISION=ON -DVTKm_USE_DEFAULT_TYPES_FOR_ASCENT=ON -DVTKm_NO_DEPRECATED_VIRTUAL=ON 
      make -j8 install
      popd

#. Ascent

   .. code:: bash

      git clone --recursive https://github.com/Alpine-DAV/ascent.git
      pushd ascent
      mkdir build && cd build/
      cmake -S ../src -DCMAKE_INSTALL_PREFIX=$ASCENT_INSTALL_DIR -DCMAKE_BUILD_TYPE=Release -DCONDUIT_DIR=$ASCENT_INSTALL_DIR -DVTKM_DIR=$ASCENT_INSTALL_DIR -DENABLE_VTKH=ON -DENABLE_FORTRAN=OFF -DENABLE_PYTHON=OFF -DENABLE_DOCS=OFF -DBUILD_SHARED_LIBS=ON
      make -j8 install
      popd


Build mfix
<<<<<<<<<<<

#. Once the above dependencies are built, clone and load the environment necessary for MFIX-Exa

   .. tabs::

      .. tab:: CPU

         .. code:: bash

            ## Clone
            git clone https://mfix.netl.doe.gov/gitlab/exa/mfix.git
            cd mfix
            git checkout develop
            git submodule update --init
            mkdir build && cd build/

            ## load the necessary modules
            module purge
            module load vgl
            module load grace
            module load cmake/3.23.1
            module load gnu/9.3.0
            module load openmpi/4.0.4_gnu9.3
            module load boost/1.77.0_gnu9.3

            export MY_INSTALL_DIR=$HOME/packages/mfix-exa_deps
            export HYPRE_DIR=$MY_INSTALL_DIR/hypre
            export HYPRE_ROOT=$HYPRE_DIR
            export HYPRE_LIBRARIES=$HYPRE_DIR/lib
            export HYPRE_INCLUDE_DIRS=$HYPRE_DIR/include

            export Boost_INCLUDE_DIR="-I/nfs/apps/Libraries/Boost/1.77.0/gnu/9.3.0/openmpi/4.0.4/include"

            export ASCENT_DIR=$MY_INSTALL_DIR/ascent
            export CONDUIT_DIR=$ASCENT_DIR
            export CMAKE_PREFIX_PATH=$CMAKE_PREFIX_PATH:$ASCENT_DIR/lib/cmake/ascent
            export CMAKE_PREFIX_PATH=$CMAKE_PREFIX_PATH:$ASCENT_DIR/lib/cmake/conduit

            export CSG_DIR=$MY_INSTALL_DIR/csg-deps
            export CMAKE_PREFIX_PATH=$CMAKE_PREFIX_PATH:$CSG_DIR

      .. tab:: GPU

         .. code:: bash

            ## Clone
            git clone https://mfix.netl.doe.gov/gitlab/exa/mfix.git
            cd mfix
            git checkout develop
            git submodule update --init
            mkdir build && cd build/

            ## load the necessary modules
            module purge
            module load vgl
            module load grace
            module load cmake/3.23.1
            module load cuda/11.3
            module load gnu/9.3.0
            module load openmpi/4.0.4_gnu9.3


            export MY_INSTALL_DIR=$HOME/mfix-exa_deps_gnu9.3_cuda11.3
            export HYPRE_DIR=$MY_INSTALL_DIR/hypre
            export HYPRE_ROOT=$HYPRE_DIR
            export HYPRE_LIBRARIES=$HYPRE_DIR/lib
            export HYPRE_INCLUDE_DIRS=$HYPRE_DIR/include

            export ASCENT_DIR=$MY_INSTALL_DIR/ascent
            export CONDUIT_DIR=$ASCENT_DIR
            export CMAKE_PREFIX_PATH=$CMAKE_PREFIX_PATH:$ASCENT_DIR/lib/cmake/ascent
            export CMAKE_PREFIX_PATH=$CMAKE_PREFIX_PATH:$ASCENT_DIR/lib/cmake/conduit

            export CSG_DIR=$MY_INSTALL_DIR/csg-deps
            export CMAKE_PREFIX_PATH=$CMAKE_PREFIX_PATH:$CSG_DIR

            export BOOST_ROOT="/nfs/apps/Libraries/Boost/1.77.0/gnu/9.3.0/openmpi/4.0.4"


#. Build MFIX-Exa

   .. tabs::

      .. tab:: CPU

         .. code:: bash

            cmake -DCMAKE_C_COMPILER=gcc \
                  -DCMAKE_CXX_COMPILER=g++ \
                  -DCMAKE_Fortran_COMPILER=gfortran \
                  -DMFIX_MPI=yes \
                  -DMFIX_OMP=no \
                  -DMFIX_GPU_BACKEND=NONE \
                  -DMFIX_CSG=yes \
                  -DMFIX_HYPRE=yes \
                  -DAMReX_ASCENT=yes \
                  -DAMReX_CONDUIT=yes \
                  -DAMReX_TINY_PROFILE=no \
                  -DCMAKE_BUILD_TYPE=Release \
                  ../mfix
            make -j 8

            ## uncomment to build the pic2dem restarter app
            #cmake -j8 --build . --target pic2dem

      .. tab:: GPU

         .. code:: bash

            cmake -DCMAKE_C_COMPILER=gcc \
                  -DCMAKE_CXX_COMPILER=g++ \
                  -DCMAKE_Fortran_COMPILER=gfortran \
                  -DBoost_INCLUDE_DIR="$BOOST_ROOT/include" \
                  -DMFIX_MPI=yes \
                  -DMFIX_OMP=no \
                  -DMFIX_GPU_BACKEND=CUDA \
                  -DAMReX_CUDA_ARCH=6.0 \
                  -DGPUS_PER_SOCKET=1 \
                  -DGPUS_PER_NODE=2 \
                  -DMFIX_CSG=yes \
                  -DMFIX_HYPRE=yes \
                  -DAMReX_ASCENT=yes \
                  -DAMReX_CONDUIT=yes \
                  -DAMReX_TINY_PROFILE=no \
                  -DCMAKE_BUILD_TYPE=Release \
                  ../mfix
            make -j 32



Building MFIX-Exa: gmake
-------------------------

Standalone Build
>>>>>>>>>>>>>>>>>

#. Clone
   
   For the basic, standalone CPU and GPU builds, first clone the code, 
   checkout the desired branch and update the submodules

   .. code:: bash

    ## Clone
    git clone https://mfix.netl.doe.gov/gitlab/exa/mfix.git
    cd mfix
    git checkout develop
    git submodule update --init

#. Build mfix
   
   For a CPU load the necessary modules and build from the head node. For GPU first connect to an interactive node and then build:
   
   .. tabs::
      
      .. tab:: CPU

         .. code:: bash

            ## load the necessary modules
            module purge
            module load vgl
            module load grace
            module load cmake/3.23.1
            module load gnu/9.3.0
            module load openmpi/4.0.4_gnu9.3

            ## build mfix
            make -C exec -j8 COMP=gnu USE_MPI=TRUE USE_OMP=FALSE USE_CUDA=FALSE USE_TINY_PROFILE=FALSE USE_CSG=FALSE USE_HYPRE=FALSE DEBUG=FALSE
            

      .. tab:: GPU

         .. code:: bash
            
            ## connect to an interactive node
            salloc -N 1 -p gpu

            ## load Modules
            module purge
            module load vgl
            module load grace
            module load cmake/3.23.1
            module load cuda/11.3
            module load gnu/9.3.0
            module load openmpi/4.0.4_gnu9.3

            ## build mfix
            make -C exec -j8 COMP=gnu USE_MPI=TRUE USE_OMP=FALSE USE_CUDA=TRUE CUDA_ARCH=6.0 USE_TINY_PROFILE=FALSE USE_CSG=FALSE USE_HYPRE=FALSE DEBUG=FALSE


Full Build
>>>>>>>>>>>

To build MFIX-Exa with hypre, csg and/or ascent dependencies, you first need to build and install these libraries and their dependencies.

#. First `Install dependencies`_

#. Then, install `libcsgeb` to `$HOME/install_csg_eb` using either cmake or gmake:

   .. tabs::

      .. tab:: cmake

         .. code:: bash

            ## Clone
            git clone https://mfix.netl.doe.gov/gitlab/exa/mfix.git
            cd mfix
            git checkout develop
            git submodule update --init
            cd subprojects/csg-eb

            ## Install library
            export Boost_INCLUDE_DIR="-I/nfs/apps/Libraries/Boost/1.77.0/gnu/9.3.0/openmpi/4.0.4/include"
            export CSG_DIR=$MY_INSTALL_DIR/csg-deps
            export CMAKE_PREFIX_PATH=$CMAKE_PREFIX_PATH:$CSG_DIR`

            cmake -S . -B build -DCMAKE_INSTALL_PREFIX=$HOME/install_csg_eb
            cd build
            make -j8 install

      .. tab:: gmake

         .. code:: bash

            ## Clone
            git clone https://mfix.netl.doe.gov/gitlab/exa/mfix.git
            cd mfix
            git checkout develop
            git submodule update --init

            ## Install library
            make -C subprojects/csg-eb install DESTDIR=$HOME/install_csg_eb \
            BOOST_HOME=/nfs/apps/Libraries/Boost/1.77.0/gnu/9.3.0/openmpi/4.0.4 \
            PEGTL_HOME=$HOME/install \
            CGAL_HOME=$HOME/install \
            CATCH2_HOME=$HOME/install \
            ENABLE_CGAL=TRUE


#. Load the necessary modules and environment
   
   .. tabs::
      
      .. tab:: CPU

         .. code:: bash

            ## load the necessary modules
            module purge
            module load vgl
            module load grace
            module load cmake/3.23.1
            module load gnu/9.3.0
            module load openmpi/4.0.4_gnu9.3

            export MY_INSTALL_DIR=$HOME/packages/mfix-exa_deps
            export HYPRE_DIR=$MY_INSTALL_DIR/hypre
            export HYPRE_HOME=$HYPRE_DIR

            export ASCENT_DIR=$MY_INSTALL_DIR/ascent
            export CONDUIT_DIR=$ASCENT_DIR

            export CSGEB_HOME=$HOME/install_csg_eb

      .. tab:: GPU

         .. code:: bash
            
            ## load the necessary modules
            module purge
            module load vgl
            module load grace
            module load cmake/3.23.1
            module load cuda/11.3
            module load gnu/9.3.0
            module load openmpi/4.0.4_gnu9.3


            export MY_INSTALL_DIR=$HOME/mfix-exa_deps
            export HYPRE_DIR=$MY_INSTALL_DIR/hypre
            export HYPRE_HOME=$HYPRE_DIR

            export ASCENT_DIR=$MY_INSTALL_DIR/ascent
            export CONDUIT_DIR=$ASCENT_DIR

            export CSGEB_HOME=$HOME/install_csg_eb


#. Build MFIX-Exa

   .. tabs::

      .. tab:: CPU

         .. code:: bash

            make -C exec -j8 COMP=gnu \
            USE_MPI=TRUE \
            USE_OMP=FALSE \
            USE_CUDA=FALSE \
            USE_TINY_PROFILE=FALSE \
            USE_CSG=TRUE \
            USE_HYPRE=TRUE \
            USE_ASCENT=TRUE \
            USE_CONDUIT=TRUE \
            DEBUG=FALSE

      .. tab:: GPU

         .. code:: bash

            make -C exec -j8 COMP=gnu \
            USE_MPI=TRUE \
            USE_OMP=FALSE \
            USE_CUDA=TRUE \
            CUDA_ARCH=6.0 \
            USE_TINY_PROFILE=FALSE \
            USE_CSG=TRUE \
            USE_HYPRE=TRUE \
            USE_ASCENT=TRUE \
            USE_CONDUIT=TRUE \
            DEBUG=FALSE
