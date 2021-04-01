# Singularity container for EIC Fun4All

Singularity container for EIC Fun4All allows any user to run the EIC RCF/SDCC environment with the nightly builds on your local computers or on external high-performance computing clusters. 

This repository is part of [the software tutorial](https://eic-detector.github.io/tutorials_landing_page.html), in particular for users offsite to [the BNL RACF computer center](https://www.racf.bnl.gov/). This repository includes the instruction and local update macro for this Singularity container, which ensures binary reproducible simulation and reconstruction.

**Daily validations:** `updatebuild.sh --build=new` 
[![Build Status](https://web.sdcc.bnl.gov/jenkins-sphenix/buildStatus/icon?job=EIC%2Fsingularity-download-validation)](https://web.sdcc.bnl.gov/jenkins-sphenix/job/EIC/job/singularity-download-validation/)

[![Macros](https://img.shields.io/badge/standard%20macros-git-green.svg)](https://github.com/eic/fun4all_macros)
[![Tutorials](https://img.shields.io/badge/tutorials-git-green.svg)](https://github.com/eic/fun4all_eic_tutorials)
[![Doxygen](https://img.shields.io/badge/code%20reference-Doxygen-green.svg)](https://eic.github.io/doxygen/)
[![Last Commit](https://img.shields.io/github/last-commit/sPHENIX-Collaboration/Singularity.svg)](https://github.com/eic/Singularity/commits/master)

# How to download

EIC Fun4All software can be obtained to your local computing environment in two ways. You can use both ways concurrently - there is no collision between these two options. Which you use is determined by the start of the singularity container (look closely - for option 1 you use /cvmfs:/cvmfs which mounts our cvmfs volume under /cvmfs, for option 2 you use cvmfs:/cvmfs which mounts your local copy under /cvmfs)

1. **[Option-1 Mount EIC CVMFS](#option-1-mount-eic-cvmfs)**: EIC singularity container, software and builds are distribute on [CVMFS](https://www.racf.bnl.gov/docs/services/cvmfs/info) since Nov 2018. Like RCF/SDCC computing cluster at BNL, external collaborating computing center could also mount the `/cvmfs/eic.opensciencegrid.org/` CVMFS repository, which automatically obtain, buffer and update all EIC Fun4All build files. `/cvmfs/eic.opensciencegrid.org` is also distributed by default throughout the [Open Science Grid](opensciencegrid.org). 
2. **[Option-2 Download sPHENIX build via HTTPS archive](#option-2-download-the-eic-fun4all-build-via-https-archive)**: one can also directly download the files for the EIC Fun4All build to a local folder via [the nightly refreshed HTTPS archive](https://sphenix.sdcc.bnl.gov/WWW/user/phnxbld/EIC/Singularity/). 

The advantage of **Option-1 Mount EIC CVMFS** is that it mounts all EIC Fun4All builds and software and performs automatic caching and updates. This would be suitable for the case of a computing center or server environment. However, it requires constant network connection to function. Therefore, if you wish to use the EIC Fun4All software on a laptop during travel, **Option-2 Downloading EIC Fun4All builds via HTTPS archive** would work best.

*Note*: although singularity containers are supported under [MacOS](#mac-installation) and Windows Linux Subsystem, it runs best under Linux. Therefore, for Windows and Mac user, it is recommended to run Option2 within a Linux virtual machine, we provide [a Unbuntu VirtualBox](VirtualBox.md) which has cvmfs and singularity enabled.

## Option-1: Mount EIC CVMFS

1. On your local system, install [Singularity](https://sylabs.io/singularity/). 

    - *Note: Singularity installation may require host to support local compilation. E.g. on Ubuntu, it can be obtained via `sudo apt-get install libtool m4 automake`*

2. Install [CVMFS from CERN](https://cernvm.cern.ch/portal/filesystem/quickstart). CERN support [build packagess](https://cernvm.cern.ch/portal/filesystem/downloads) for various Linux distributions and MAC
    - *Note: for loading `/cvmfs/eic.opensciencegrid.org` by default, you may need to add `CVMFS_STRICT_MOUNT=no` to `/etc/cvmfs/default.local`.*

   After completing this, please confirm you can read local path `/cvmfs/eic.opensciencegrid.org/`, which should show same content as that on RCF interactive nodes. 
   
3. launch singularity container for EIC Fun4All with following command

```
singularity shell -B /cvmfs:/cvmfs /cvmfs/eic.opensciencegrid.org/singularity/rhic_sl7_ext.simg
source /cvmfs/eic.opensciencegrid.org/x8664_sl7/opt/fun4all/core/bin/eic_setup.sh -n   # setup EIC Fun4All environment in the singularity container shell. Note the shell is bash by default
root # give a test
```

*For Singularity v3+, in particular CERN computing users: `rhic_sl7_ext.simg` might be slow to load under certain Singularity security settings at your computing center. In that case, please load with an alternative image: `singularity shell -B /cvmfs:/cvmfs /cvmfs/eic.opensciencegrid.org/singularity/rhic_sl7_ext`*


As an example, for Ubuntu hosts, here is a set of commands executing Option-1

```bash
sudo apt-get install gcc perl make

# install cvmfs
# https://cernvm.cern.ch/portal/filesystem/quickstart
sudo apt-get install lsb-release
wget https://ecsft.cern.ch/dist/cvmfs/cvmfs-release/cvmfs-release-latest_all.deb
sudo dpkg -i cvmfs-release-latest_all.deb 
rm -f cvmfs-release-latest_all.deb 
sudo apt-get update sudo 
apt-get install cvmfs cvmfs-config-default 
sudo cvmfs_config setup 

sudo emacs  /etc/cvmfs/default.local # adapt to include following two lines
     CVMFS_REPOSITORIES=eic.opensciencegrid.org[,others] 
     CVMFS_HTTP_PROXY=DIRECT
     
cvmfs_config probe

# singularity
sudo apt install singularity singularity-container singularity 

singularity shell -B /cvmfs:/cvmfs /cvmfs/eic.opensciencegrid.org/singularity/rhic_sl7_ext.simg

source /cvmfs/eic.opensciencegrid.org/x8664_sl7/opt/fun4all/core/bin/eic_setup.sh -n   # setup EIC Fun4All environment in the singularity container shell. Note the shell is bash by default

root # give a test
```


## Option-2: Download the EIC Fun4All build via HTTPS archive


1. On your local system, install [Singularity](https://sylabs.io/singularity/). 

    - *Note: Singularity installation may require host to support local compilation. E.g. on Ubuntu, the compilation tools can be obtained via `sudo apt-get install libtool m4 automake`*

2. Download this repository:

```
git clone https://github.com/eic/Singularity.git
cd Singularity/
```

3. Run the download/update macro [updatebuild.sh](./updatebuild.sh).

```
./updatebuild.sh
```

This script downloads the current release of the EIC Singularity container and up to date versions of our software. The total download size is about 5 GB  and the decompressed disk usage is about 10 GB. Two build versions are supported with default as the `new` build with`./updatebuild.sh`. The new build with gcc 8.3 can be downloaded using `./updatebuild.sh --sysname=gcc-8.3`


4. Start the container with 

```
singularity shell -B cvmfs:/cvmfs cvmfs/eic.opensciencegrid.org/singularity/rhic_sl7_ext.simg
source /cvmfs/eic.opensciencegrid.org/x8664_sl7/opt/fun4all/core/bin/eic_setup.sh -n   # setup EIC environment in the singularity container shell. Note the shell is bash by default
root # give a test
```
*if you get an error, start singularity with --writable like* 

```
singularity shell --writable -B cvmfs:/cvmfs cvmfs/eic.opensciencegrid.org/singularity/rhic_sl7_ext.simg
```

*Please note the slight difference in singularity shell commands for option 1 and option 2 (/cvmfs versus cvmfs)*

5. To get daily build update, run the download/update macro [updatebuild.sh](./updatebuild.sh) to sync build files again. 


# What's next

After entering the Singularity container, you can source the EIC environment and interact with it in the same way as on RCF: 

```
computer:~/> singularity shell <options depending on which of the two downloading options above>
Singularity: Invoking an interactive shell within container...
Singularity rhic_sl7_ext.simg:~/> source  /cvmfs/eic.opensciencegrid.org/x8664_sl7/opt/fun4all/core/bin/eic_setup.sh -n
# or source /cvmfs/eic.opensciencegrid.org/gcc-8.3/opt/sphenix/core/bin/sphenix_setup.sh -n # if using gcc v8
Singularity rhic_sl7_ext.simg:~/> lsb_release  -a         # Verify same environment shows up as that on RCF
LSB Version:	:core-4.1-amd64:core-4.1-ia32:core-4.1-noarch
Distributor ID:	Scientific
Description:	Scientific Linux release 7.3 (Nitrogen)
Release:	7.3
Codename:	Nitrogen
```

This bring up a shell environment which is identical to sPHENIX RCF. Meanwhile, it use your local file system for non-system files, e.g. it directly work on your code or data directories. Singularity container also support running in the [command mode or background mode](https://sylabs.io/docs/). 

Next, please try [the software tutorial](https://eic-detector.github.io/tutorials_landing_page.html). 

# Troubleshooting

## MacOS installation

Singularity runs under linux OS. But in macOS, it require another layer of virtual machine to generate a linux environment first ([read more on Singularity docs](https://www.sylabs.io/guides/2.5/user-guide/quick_start.html#installation)). The easiest approach would be using the container [within a Unbuntu VirtualBox](VirtualBox.md). Alternatively, Here is [a more detailed guild on the macOS installation of the EIC container](./OSX_installationguide.md). 

## Windows installation

The easiest approach is to install [Virtual Box](https://www.virtualbox.org/) running our [within a Unbuntu VirtualBox](VirtualBox.md).

## 3D accelerated Graphics

The container is built for batch computing. It could be tricky to bring up 3D-accelerated graphics for Geant4 display. 
* On Linux, binding local X IPC socket folder to the singularity container could help enabling local hardware 3D acceleration, e.g. `singularity -B /tmp/.X11-unix:/tmp/.X11-unix ....` followed with `setenv DISPLAY unix:0.0` in the container. 
* On MacOS and Windows, accelerated graphics runs well with [a Linux VirtualBox](VirtualBox.md) with graphical acceleration enabled in the VirtualBox. 
* On MacOS John H. have developed [a note on how to get the 3D graphics working on MAC](https://indico.bnl.gov/event/4046/contributions/25558/attachments/21219/28796/singularity_mac_haggerty_20181217.pdf). 

## Clean download in Option-2

Occasionally, local download cache can become corrupt, e.g. after an interrupted `updatebuild.sh` call. If you encounter a problem executing container, it is always useful to first try clean up the local download buffer by removing the `./cvmfs` folder and download again. Or you can run `./updatebuild.sh --clean <other options>` which force a clean download (default is incremental updates). 

If you have RCF credentials, you can also compare your local output with the daily test runs of the default simulation macro in the container following these links: `updatebuild.sh --build=new` 
[![Build Status](https://web.sdcc.bnl.gov/jenkins-sphenix/buildStatus/icon?job=sPHENIX%2Fsingularity-download-validation)](https://web.sdcc.bnl.gov/jenkins-sphenix/job/sPHENIX/job/singularity-download-validation/) 
