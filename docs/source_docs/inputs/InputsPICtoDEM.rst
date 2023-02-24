.. _Chap:InputsPICtoDEM:

PIC to DEM conversion
=====================

The PIC to DEM tool allows to convert a checkpoint file obtained from a PIC run
into a new checkpoint file on a custom-refined mesh with DEM particles. The DEM
particles positions are initialized such that the original PIC parcels
distribution (aka the solids volume fraction) is preserved in each cell up to
a negligible error.

Building the PIC to DEM tool using CMake (from inside the build directory):

::

    cmake --build ./ --target pic2dem

The following inputs must be preceded by "pic2dem".

+------------------+---------------------------------------------------+--------+---------+
|                  | Description                                       | Type   | Default |
+==================+===================================================+========+=========+
| refinement_ratio | The mesh refinement ratio that will be applied    | int    | 1       |
|                  | on each direction to obtain a refined mesh out of |        |         |
|                  | the one  read form the PIC checkpoint file        |        |         |
+------------------+---------------------------------------------------+--------+---------+
| eps_tolerance    | The threshold used for DEM particles generation   | Real   | 1.e-15  |
|                  | from the PIC solids volfrac distribution. If ep_s |        |         |
|                  | in a cell is below the threshold, no DEM solids   |        |         |
|                  | will be generated in that cell                    |        |         |
+------------------+---------------------------------------------------+--------+---------+
| eps_overflow     | An artifact, like a knob, for the user to force   | Real   | 1.0     |
|                  | over-production (if value > 1), or                |        |         |
|                  | under-production (if value < 1) of DEM particles  |        |         |
+------------------+---------------------------------------------------+--------+---------+

The following inputs must be preceded by "eb2".

+-----------------------+----------------------------------------------+--------+---------+
|                       | Description                                  | Type   | Default |
+=======================+==============================================+========+=========+
| refined_small_volfrac | This input gives the possibility to set a    | Real   | 0.      |
|                       | small volfrac value just for the DEM refined |        |         |
|                       | case, while the standard small volfrac value |        |         |
|                       | will still be used for the PIC case read     |        |         |
|                       | from the checkpoint file                     |        |         |
+-----------------------+----------------------------------------------+--------+---------+

The following inputs must be preceded by "amr".

+-------------------------------+--------------------------------------+--------+---------+
|                               | Description                          | Type   | Default |
+===============================+======================================+========+=========+
| refined_geom_chk_file         | Name of the EB checkpoint file where | string | ""      |
|                               | the DEM refined case geometry will   |        |         |
|                               | be read or written                   |        |         |
+-------------------------------+--------------------------------------+--------+---------+
| refined_geom_chk_refined_file | Name of the EB checkpoint file where | string | ""      |
|                               | the finer levels of the geometry for |        |         |
|                               | the DEM refined case will be read or |        |         |
|                               | written                              |        |         |
+-------------------------------+--------------------------------------+--------+---------+
| refined_geom_chk_write        | Flag to turn on/off writing the EB   | bool   | false   |
|                               | geometry checkpoint file for the DEM |        |         |
|                               | refined case                         |        |         |
+-------------------------------+--------------------------------------+--------+---------+
| refined_geom_chk_read         | Flag to turn on/off reading the EB   | bool   | false   |
|                               | geometry checkpoint file for the DEM |        |         |
|                               | refined case                         |        |         |
+-------------------------------+--------------------------------------+--------+---------+

The following inputs must be preceded by "pic2dem".

+-------------------+--------------------------------------------------+--------+---------+
|                   | Description                                      | Type   | Default |
+===================+==================================================+========+=========+
| geometry_filename | CSG filename that will be used for generating    | string | ""      |
|                   | the DEM refined geometry in case the PIC coarse  |        |         |
|                   | geometry is read from  a checkpoint file         |        |         |
+-------------------+--------------------------------------------------+--------+---------+
