# Using EIC Fun4All software with an Ubuntu Virtual Box

[Singularity container](./README.md) for Fun4All for the EIC allow collaborators to run the EIC RCF/SDCC environment with the nightly builds on your local computers or on external high-performance computing clusters. It is optimized for offsite computing on Linux-based systems.

On Windows or MacOS PC/laptop though, it could be tricky to run Singularity directly on the host system. Therefore, this page provide a solution to run EIC Singularity container under an Linux Ubuntu [virtual box](https://www.virtualbox.org/wiki/Downloads), which can run well on Windows or MacOS. 

1. Install VirtualBox on your host system: [Virtual Box](https://www.virtualbox.org/)

2. Download [EIC Ubuntu 18.04LTS Image](https://www.phenix.bnl.gov/WWW/publish/phnxbld/ECCE/Singularity/Fun4AllSingularityDistribution.ova). The MD5SUM is `87a6464dbae831724bc9eb3b508f2646`.

This image has CVMFS and Singularity3 preinstalled, enabling direct loading all EIC Fun4All builds via the OpenScienceGrid network (i.e. Option-1 of [main documentation](/README.md)).   

3. Import the above image to your virtual box ([HowTs](https://www.google.com/search?q=Virtal+box+import+ova)). Please ensure you have internet connection before the next step (e.g. visit www.bnl.gov from inside the VirtualBox)

Then you are good to go. Start the imported Virtual Box. **The Ubuntu VM is set to do unattended-upgrades. On the first start it will pull in and install a lot of package updates**. The default user is `fun4all` with password of `fun4all`. It would be good practice to [change at least the password](https://www.google.com/search?q=ubuntu+howto+change+password) after first login. 

Following files should be installed in the home folder:

```
cp  /cvmfs/eic.opensciencegrid.org/singularity/fun4all_scripts/ecce/singularity_shell.sh ~/
cp /cvmfs/eic.opensciencegrid.org/singularity/fun4all_scripts/ecce/setup.sh ~/
```

Then we have:

* `~/singularity_shell.sh`: execute this script to get a bash shell with the Singularity container.
* `~/setup.sh`: source this macro from inside the Singularity container to use the lastest build 
* `~/install/`: local build folder to be used for `$MYINSTALL` area, which is currently empty

*Plesae note the first use of CVMFS may be slow as it synchronizes online*

Start using it with an Ubuntu terminal:
```
fun4all@Fun4AllSingularity:~$ ./singularity_shell.sh
entering the Fun4All singularity container on /cvmfs/eic.opensciencegrid.org


Singularity> source setup.sh 
.....
Using the Fun4All build at /cvmfs/eic.opensciencegrid.org/gcc-8.3/release/release_new/new.3 (the build version number may be different, it is 1-4)
Local build will be installed at /home/fun4all/install

Singularity> git clone https://github.com/ecce-eic/macros.git
Cloning into 'macros'...
...

Singularity> cd macros/detectors/EICDetector
Singularity> root Fun4All_G4_EICDetector.C
   ------------------------------------------------------------
  | Welcome to ROOT 6.22/02                  https://root.cern |
...
```
Then you are welcome to follow the default macro tutorials: https://github.com/ecce-eic/macros

Please note the first use of a day will be slow, as CVMFS caches files from BNL SDCC over the network. You are also welcome use [Option-2 downloading a build and run the software without internet](/README.md#option-2-download-sphenix-build-via-https-archive). In this case you have to start the singularity container via
```
singularity shell -B cvmfs:/cvmfs cvmfs/eic.opensciencegrid.org/singularity/rhic_sl7_ext
```

![Screenshot with Fun4All_G4_EICDetector.C](screenshot.png)

# Troubleshooting

If you are having display issues then you can tell virtualbox to use a different graphics support by going to Settings->Display->Graphics Controller and selecting VboxVGA (You can also disable 3D acceleration here)

![Changing Graphics Support](screenshotGraphicsSupport.png)
