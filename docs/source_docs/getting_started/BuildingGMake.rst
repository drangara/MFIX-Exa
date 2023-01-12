Building MFiX-Exa with gmake
============================

If you want to use gmake to build MFiX_Exa, you will need to
clone amrex and AMReX-Hydro into a local directories, and also
clone mfix:

.. code:: shell

    > git clone https://github.com/AMReX-Codes/amrex.git
    > git clone https://github.com/AMReX-Codes/AMReX-Hydro.git
    > git clone http://mfix.netl.doe.gov/gitlab/exa/mfix.git
    > cd mfix/exec

Then, edit the GNUmakefile (or set an environment variable)
to define AMREX_HOME and AMREX_HYDRO_HOME
to be the path to the directories where you have put amrex
and AMReX-Hydro.  Note that the default locations of
AMREX_HOME and AMREX_HYDRO_HOME are in the subprojects directory,
which is where a cmake configuration may clone these repositories.
Other options that you can set include

+-----------------+----------------------------------+------------------+-------------+
| Option name     | Description                      | Possible values  | Default     |
|                 |                                  |                  | value       |
+=================+==================================+==================+=============+
| COMP            | Compiler (gnu or intel)          | gnu / intel      | gnu         |
+-----------------+----------------------------------+------------------+-------------+
| USE_MPI         | Enable MPI                       | TRUE / FALSE     | FALSE       |
+-----------------+----------------------------------+------------------+-------------+
| USE_OMP         | Enable OpenMP                    | TRUE / FALSE     | FALSE       |
+-----------------+----------------------------------+------------------+-------------+
| USE_CSG         | Enable CSG geometry file support | TRUE / FALSE     | FALSE       |
+-----------------+----------------------------------+------------------+-------------+
| USE_CUDA        | Enable CUDA GPU support          | TRUE / FALSE     | FALSE       |
+-----------------+----------------------------------+------------------+-------------+
| USE_HIP         | Enable HIP GPU support           | TRUE / FALSE     | FALSE       |
+-----------------+----------------------------------+------------------+-------------+
| USE_DPCPP       | Enable DPCPP GPU support         | TRUE / FALSE     | FALSE       |
+-----------------+----------------------------------+------------------+-------------+
| USE_HYPRE       | Enable HYPRE support             | TRUE / FALSE     | FALSE       |
+-----------------+----------------------------------+------------------+-------------+
| DEBUG           | Create a DEBUG executable        | TRUE / FALSE     | FALSE       |
+-----------------+----------------------------------+------------------+-------------+
| PROFILE         | Include profiling info           | TRUE / FALSE     | FALSE       |
+-----------------+----------------------------------+------------------+-------------+
| TRACE_PROFILE   | Include trace profiling info     | TRUE / FALSE     | FALSE       |
+-----------------+----------------------------------+------------------+-------------+
| COMM_PROFILE    | Include comm profiling info      | TRUE / FALSE     | FALSE       |
+-----------------+----------------------------------+------------------+-------------+
| TINY_PROFILE    | Include tiny profiling info      | TRUE / FALSE     | FALSE       |
+-----------------+----------------------------------+------------------+-------------+
| DIM             | Dimensionality of problem        | 2 / 3            | 3           |
+-----------------+----------------------------------+------------------+-------------+

.. note::
   **Do not set both USE_OMP and USE_CUDA/HIP/DPCPP to true.**

Then type

.. code:: shell

    > make -j

An executable will appear; the executable name will reflect
some of the build options above.
