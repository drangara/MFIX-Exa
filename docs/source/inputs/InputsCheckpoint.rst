.. _Chap:InputsCheckpoint:

Checkpoint/Restart
==================

The following inputs must be preceded by "amr" and control checkpoint/restart.

+-------------------------+-----------------------------------------------------------------------+-------------+-----------+
|                         | Description                                                           |   Type      | Default   |
+=========================+=======================================================================+=============+===========+
| restart                 | If present, then the name of file to restart from                     |    String   | None      |
+-------------------------+-----------------------------------------------------------------------+-------------+-----------+
| check_int               | Frequency of checkpoint output;                                       |    Int      | -1        |
|                         | if -1 then no checkpoints will be written                             |             |           |
+-------------------------+-----------------------------------------------------------------------+-------------+-----------+
| check_file              | Prefix to use for checkpoint output                                   |  String     | chk       |
+-------------------------+-----------------------------------------------------------------------+-------------+-----------+
| check_walltime          | Write a check point file after the specified walltime (HH:MM:SS)      |  String     | None      |
|                         | has lapsed. For example, if amr.check_waltime = 00:10:00, then a      |             |           |
|                         | checkpoint file is after a job has run for ten minutes.               |             |           |
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
