Introduction
================

This project describes how to integrate DMTCP checkpointing into gridengine.

Installation
============

Install the various dmtcp\_\* scripts onto all of the executions hosts.  I
install into /usr/share/gridengine/util, and I distribute them via puppet.

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
    ckpt_dir           /data/cora/dmtcp
    signal             NONE
    when               xsr

Save the above to a file (say dmtcp) and then run:

    qconf -Ackpt dmtcp

Finally, you need to set the starter\_method and checkpoint environment or all 
queues that are going to use dmtcp checkpointing.  Run:

    qconf -mq queue

For each queue that will use dmtcp checkpointing and set:

    ckpt_list             dmtcp
    starter_method        /usr/share/gridengine/util/dmtcp_starter
