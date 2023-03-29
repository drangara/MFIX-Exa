.. _Chap:InputsCheckpoint:

Checkpoint/Restart
==================

The following inputs must be preceded by "mfix." and control checkpoint/restart.

+-------------------------+-----------------------------------------------------------------------+-------------+-----------+
|                         | Description                                                           |   Type      | Default   |
+=========================+=======================================================================+=============+===========+
| restart                 | If present, then the name of file to restart from                     |    String   | None      |
+-------------------------+-----------------------------------------------------------------------+-------------+-----------+
| check_int               | Frequency of checkpoint output in timesteps number;                   |    Int      | -1        |
|                         | if -1 then no checkpoints will be written                             |             |           |
+-------------------------+-----------------------------------------------------------------------+-------------+-----------+
| check_per_approx        | Frequency of checkpoint output in simulation time;                    |    Real     | 0         |
|                         | if 0 then no checkpoints will be written                              |             |           |
+-------------------------+-----------------------------------------------------------------------+-------------+-----------+
| check_file              | Prefix to use for checkpoint output                                   |  String     | chk       |
+-------------------------+-----------------------------------------------------------------------+-------------+-----------+
| check_walltime_limit    | Write a check point file before the specified walltime (HH:MM:SS)     |    Real     | -1        |
|                         | has been reached. For example, if mfix.check_walltime_limit = 0:10:00 |             |           |
|                         | then a checkpoint file is written a little time before the job has    |             |           |
|                         | reached 10 minutes of runtime                                         |             |           |
+-------------------------+-----------------------------------------------------------------------+-------------+-----------+
| check_walltime_buffer   | This is time subtracted to check_walltime_limit to avoid the job is   |    Real     | 300       |
|                         | killed before mfix has completed writing the checkpoint file          |             |           |
+-------------------------+-----------------------------------------------------------------------+-------------+-----------+
| geom_chk_write          | When True, writes the EB geometry data into geom_chk_file             |  bool       | False     |
|                         | and additionally, geom_refined_chk_file, if levelset                  |             |           |
|                         | refinement is enabled.                                                |             |           |
+-------------------------+-----------------------------------------------------------------------+-------------+-----------+
| geom_chk_read           | When True, reads the EB geometry data from geom_chk_file              |  bool       | False     |
|                         | and additionally, geom_refined_chk_file, if levelset                  |             |           |
|                         | refinement is enabled.                                                |             |           |
+-------------------------+-----------------------------------------------------------------------+-------------+-----------+
| geom_chk_file           | Name of the EB checkpoint file that is used to store or read          |  String     | geom_chk  |
|                         | the unrefined geometry.                                               |             |           |
+-------------------------+-----------------------------------------------------------------------+-------------+-----------+
| geom_refined_chk_file   | Name of the EB checkpoint file that is used to store or read          |  String     | geom\_    |
|                         | the refined geometry, i.e. when levelset refinement is enabled.       |             | refined\_ |
|                         |                                                                       |             | chk       |
+-------------------------+-----------------------------------------------------------------------+-------------+-----------+
