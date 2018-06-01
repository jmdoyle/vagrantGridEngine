vagrantGridEngine
=================

Automatic installation of Grid Engine packages for testing purposes via Vagrant on virtual machines

Here are the prerequisites:

- Having your own laptop / desktop with MacOS X / Linux or Windows (untested) running
- Having free VirtualBox installed
- Having free Vagrant installed
- Having git version management installed (or alternative you can copy the files from my github account manually)
- Having free Grid Engine tar.gz packages (I assume here ge-8.5.4-demo-common.tar.gz and ge-8.5.4-demo-bin-lx-amd64.tar.gz which you can get for free here: <http://www.univa.com/resources/univa-grid-engine-trial.php>. Update VERSION in installation.sh if you use another version)

Once you have all the files in a own directory (including the ge-8.5.4... packages!)
just run:

    vagrant up

A Vagrant "box" (VM image based on CentOS 6.5 is downloaded from Vagrant repository - but just one time)
is created and 3 virtual machines are created under Virtual Box (master, execd1, execd2).

In order to use your cluster, just type

    vagrant ssh master

The password for user vagrant is always "vagrant".

Then you can do qhost, qstat -f, qsub -b y sleep 120, qstat -j \<jobid\> etc.

Have fun!

Note that this just a quick hack to get it running and it is not perfect.

Daniel

Example
-------

On master run an example script:

    qsub $SGE_ROOT/examples/jobs/sleeper.sh

This just sleeps for 60s on the execution host. Check that it is running:

    qstat

The state will initially be 'qw' but become 'r' after a few seconds.

It will output two files to your home directory (the number is the job id):

- Sleeper.e3
- Sleeper.o3

Try out docker (create a simple script called myjob.sh first):

    qsub -l docker -soft -l docker_images="*golang:latest*" -o /dev/null -j y myjob.sh

This will download the golang:latest image on one of the hosts. Test this docker container:

    qsub -b y -l docker,docker_images="*golang:latest*" -wd /nfs -xdv /nfs:/nfs,/etc:/etc -S /bin/sh /bin/sleep 123

You can manually log on and download the image to each host. UGE will only allocate the above job to those hosts that have the image.
