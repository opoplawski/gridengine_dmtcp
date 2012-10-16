Introduction
================

This project describes how to integrate DMTCP checkpointing into gridengine.

Installation
============

Install the various dmtcp\_\* scripts onto all of the executions hosts.  I
install into /usr/share/gridengine/util and distribute them via puppet.

Configuration
=============

You need to define the dmtcp checkpointing environment.  You will need to
change the paths to reflect where you installed the scripts.  You will also
need to set ckpt\_dir to point to a shared directory accessible on all execution
hosts.

    ckpt_name          dmtcp
    interface          APPLICATION-LEVEL
    ckpt_command       /usr/share/gridengine/util/dmtcp_checkpoint
    migr_command       /usr/share/gridengine/util/dmtcp_migrate
    restart_command    NONE
    clean_command      /usr/share/gridengine/util/dmtcp_cleanup
    ckpt_dir           /nfs/dmtcp
    signal             NONE
    when               xsr

Save the above to a file (say dmtcp) and then run:

    qconf -Ackpt dmtcp

Create your ckpt\_dir with something like:

    mkdir /nfs/dmtcp
    chmod 1777 /nfs/dmtcp

Finally, you need to set the starter\_method and checkpoint environment or all 
queues that are going to use dmtcp checkpointing.  Run:

    qconf -mq queue

For each queue that will use dmtcp checkpointing and set:

    ckpt_list             dmtcp
    starter_method        /usr/share/gridengine/util/dmtcp_starter

CPU Restriction
===============

NOTE: This will not work for task jobs!

For code compiled with the Intel compilers with various -ax flags, it is important that 
the job remain on a machine with equivalent capability.  In order to do this we specify
a number of cpuflag_\* complexes:

    cpuflag_avx         cfsa        BOOL        ==    YES         NO         0        0
    cpuflag_pni         cfsp        BOOL        ==    YES         NO         0        0
    cpuflag_sse         cfs         BOOL        ==    YES         NO         0        0
    cpuflag_sse2        cfs2        BOOL        ==    YES         NO         0        0
    cpuflag_sse3        cfs3        BOOL        ==    YES         NO         0        0
    cpuflag_sse4_1      cfs41       BOOL        ==    YES         NO         0        0
    cpuflag_sse4_2      cfs42       BOOL        ==    YES         NO         0        0

We add a new checkpoint environment dmtcp_cpu which uses a different migration script:

    ckpt_name          dmtcp_cpu
    interface          APPLICATION-LEVEL
    ckpt_command       /usr/share/gridengine/util/dmtcp_checkpoint
    migr_command       /usr/share/gridengine/util/dmtcp_migrate_cpu
    restart_command    NONE
    clean_command      /usr/share/gridengine/util/dmtcp_cleanup
    ckpt_dir           /data/cora/dmtcp
    signal             NONE
    when               xsr

You need to specify the cpuflags available on the different machines.  This could be done
on a per host, queue, or by load sensor (if the machines are not shut down when idle).

All hosts with dmtcp_cpu checkpointing need to be submit or admin hosts to be able to run
qstat/qalter in the migration script.

License
=======

Copyright 2012 Orion Poplawski <orion@cora.nwra.com>

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program.  If not, see <http://www.gnu.org/licenses/>.
