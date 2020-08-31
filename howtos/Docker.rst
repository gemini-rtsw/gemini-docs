Using Docker/Podman 
===================

Introduction
------------
Citing `wikipedia <https://en.wikipedia.org/wiki/Docker_%28software%29>`_, "docker (as well as podman) is a set of platform as a service (PaaS) products that uses OS-level virtualization to deliver software in packages called containers. Containers are isolated from one another and bundle their own software, libraries and configuration files; they can communicate with each other through well-defined channels. All containers are run by a single operating system kernel and therefore use fewer resources than virtual machines."

This lets docker and similar approaches like podman be a good solution for various tasks in the new ADE generation, presumably.

ADE version 1.x
---------------
Setup
^^^^^
Installation
************
For exact installation instructions refer to descriptions of the host operating system. One of the advantages of the usage of docker is namely the fact, that docker containers work abstracted from the host OS. Note in this context that podman is syntax-compatible to docker, so if prodman is (or will be) preferred, substitute :code:`docker` with :code:`podman` in the following. I made the experience, though, that - at least under Ubuntu - podman needs quite long (several dozens of seconds) to start up, while docker starts execution almost immediately. Further investigation is needed here, because conceptwise, podman seems to be much safer and convincing, as no root level service is needed to manage image execution and associated actions; however, running
containers as non-root seems to be a bit complicated. If :code:`podman` is used, it might be that the respecitve commands
need to be prepended by :code:`sudo` to execute them in at root level nevertheless. A solution for the experienced problems
which are probably resulting from a incomplete setup will be provided once the new ADE goes into production.

Definition
**********
A `Dockerfile <https://docs.docker.com/engine/reference/builder/>`_ is used to automatically create a docker image from a ASCII text file. The following one for initial tests was used to create an image (:code:`centos7` with tag :code:`ADE`) based on the current ADE - which is copied over to the image  at creation time with all necessary tools and EPICS modules readily installed - and a centos7 base image distributed via `dockerhub <https://hub.docker.com/_/centos>`_:

::

  FROM centos:7
  
  # add local ADE installation to image. 
  # TODO: install following 'official' process
  COPY ./gem_sw /gem_sw
  COPY ./ade.sh /etc/profile.d/
  RUN groupadd -g600 gemdev
  RUN groupadd -g980 gembuild
  RUN groupadd -g993 local-admins
  RUN groupadd -g994 local-users
  RUN groupadd -g1502 ssh-users
  RUN groupadd -g1506 detlab
  RUN groupadd -g3624 software
  RUN groupadd -g2000 gemini
  RUN useradd -u600 -g600 gemdev
  
  
  RUN yum -y update; yum clean all
  
  RUN yum -y install gcc gcc-c++ glibc-devel make git cmake3 java-1.8.0-openjdk-headless readline-devel; yum clean all
  
  RUN yum -y install iproute net-tools; yum clean all
  
  CMD ["/bin/bash"]

Building
********
A docker image can be `built <https://docs.docker.com/engine/reference/commandline/build/>`_ in the directory where the Dockerfile resides, for example by executing the command:
::

  docker build -t centos7:ADE .
  
Usage
^^^^^
The docker image is now known by your docker platform and can be used. One example is of course, to use it to build a IOC module with the ADE installed in this docker image, with the sources not residing in the docker image, but on your host file system. With the following command, the gcal ioc modules was built with its code at :code:`$PWD/gcal` mounted into the docker image at startup time and hence shared between host and guest OS:
::
  
  docker run --rm -v $PWD/gcal:/gem_sw/work/R3.14.12.8/ioc/gcal -w /gem_sw/work/R3.14.12.8/ioc/gcal/mk -e EPICS=/gem_sw/epics/R3.14.12.8/ -i -t centos7:ADE /bin/bash -c ". /etc/profile && make distclean uninstall all"
  
To simply get a :code:`bash` to do things more interactively on this project you might just execute
::

  docker run --rm -v $PWD/gcal:/gem_sw/work/R3.14.12.8/ioc/gcal -w /gem_sw/work/R3.14.12.8/ioc/gcal/mk -e EPICS=/gem_sw/epics/R3.14.12.8/ -i -t centos7:ADE /bin/bash

Please refer to the respective `documentation <https://docs.docker.com/engine/reference/run/>`_ for a reference of the various parameters used with these commands. 

Hosting Image on a Registry
^^^^^^^^^^^^^^^^^^^^^^^^^^^
Apart from `dockerhub <https://hub.docker.com/_/centos>`_, the ready built images can be hosted on an own socalled `registry <https://docs.docker.com/registry/>`_. In Gemini's context, this could ensure a well-defined build environment which could be used locally to develop with ADE and to deploy RPMs, for example, after building a local image derived from the base image provided via that registry. Like :code:`centos:7` in the given example, a self-created image could be used as base for various images with specific tasks in Gemini's development environment. 

Running RTMES in ADE
--------------------
Introduction
^^^^^^^^^^^^

For the future ADE there are plans to use a environment based on :code:`mock` to create RPMs pushed to the RPM repository in an fully automated manner after creating a tag for the respective sources managed in :code:`git`. Since this new ADE will be hosted
on a CentOS8 machine, :code:`podman` will be used to manage respective containers which will be used by :code:`mock` to bootstrap
its :code:`chroot` environments. We use podman containers as a base because it is very complex to create RPMs for :code:`RTEMS`
which is build using a very unstandardized and static build process.

Setup
^^^^^
Installation
************
:code:`podman` has to be installed to the CentOS8 environment.

::

 yum -y install podman

Please note that the containers use quite some space on the drive. It is advised to have some 100GB storage for :code:`/va/lib/containers` available, maybe an extra poartition with this size.

Definition
**********
The current :code:`Containerfile` is availbale under :code:`git@gitlab.gemini.edu:rtsw/containers.git`. Its content looks similar to the 
following:

::

 FROM centos:8
 
 
 RUN mkdir -p /gem_base/targetOS/RTEMS/source
 USER root
 RUN groupadd gembuild
 RUN chgrp -R gembuild /gem_base
 RUN chmod -R g+s /gem_base
 RUN chmod -R 775 /gem_base
 # RUN usermod -a -G gembuild <username>
 
 RUN yum -y install epel-release
 RUN yum -y install dnf-plugins-core
 RUN yum config-manager --set-enabled PowerTools
 
 RUN yum -y install gcc gcc-c++ glibc-devel make git cmake3 java-1.8.0-openjdk-headless readline-devel
 
 RUN yum -y install iproute net-tools
 
 RUN yum -y install wget make automake autoconf python3 python3-devel bison flex gcc gcc-c++ texinfo patch git readline-devel re2c java; alternatives --set python /usr/bin/python3
 # RUN yum -y install make automake autoconf python3 python3-devel bison flex gcc gcc-c++; alternatives --set python /usr/bin/python3
 RUN yum -y install unzip
 RUN yum -y install bzip2
 RUN yum -y install texinfo
 RUN yum clean all
 
 # install cross-compiler toolchain
 RUN cd /gem_base/targetOS/RTEMS/source && \
  git clone -b 4.10 git://git.rtems.org/rtems-source-builder.git && \
  cd rtems-source-builder/rtems && \
  ../source-builder/sb-set-builder --log=l-powerpc.txt --without-rtems --prefix=/gem_base/targetOS/RTEMS/rtems-4.10 4.10/rtems-powerpc
 
 # install RTEMS
 RUN export PATH=/gem_base/targetOS/RTEMS/rtems-4.10/bin:$PATH && \
  cd /gem_base/targetOS/RTEMS/source && \
  mkdir rtems && \
  cd rtems && \
  wget --passive-ftp --no-directories --retr-symlinks https://git.rtems.org/rtems/snapshot/rtems-4.10.2.tar.bz2 && \
  tar xjvf rtems-4.10.2.tar.bz2 && \
  cd /gem_base/targetOS/RTEMS/source/rtems/rtems-4.10.2 && \
  ./bootstrap && \
  mkdir build && \
  cd build && \
  ../configure --target=powerpc-rtems4.10 --prefix=/gem_base/targetOS/RTEMS/rtems-4.10 --enable-cxx --enable-rdbg --disable-tests --enable-networking --enable-posix --enable-rtemsbsp="beatnik mvme2307 mvme3100 qemuppc" && \
  make && \
  make install
 
 # install RTEMS addons
 RUN export PATH=/gem_base/targetOS/RTEMS/rtems-4.10/bin:$PATH && \
  cd /gem_base/targetOS/RTEMS/source/rtems && \
  wget https://ftp.rtems.org/pub/rtems/releases/4.10/4.10.2/rtems-addon-packages-4.10.2.tar.bz2 && \
  tar xjvf rtems-addon-packages-4.10.2.tar.bz2 && \
  cd rtems-addon-packages-4.10.2 && \
  export RTEMS_MAKEFILE_PATH=/gem_base/targetOS/RTEMS/rtems-4.10/powerpc-rtems4.10/beatnik && \
  ./bit && \
  export RTEMS_MAKEFILE_PATH=/gem_base/targetOS/RTEMS/rtems-4.10/powerpc-rtems4.10/mvme2307 && \
  ./bit && \
  export RTEMS_MAKEFILE_PATH=/gem_base/targetOS/RTEMS/rtems-4.10/powerpc-rtems4.10/mvme3100 && \
  ./bit && \
  export RTEMS_MAKEFILE_PATH=/gem_base/targetOS/RTEMS/rtems-4.10/powerpc-rtems4.10/qemuppc && \
  ./bit
 
 
 # install BSP
 RUN export PATH=/gem_base/targetOS/RTEMS/rtems-4.10/bin:$PATH && \
  cd /gem_base/targetOS/RTEMS/source/rtems && \
  wget --passive-ftp --no-directories --retr-symlinks http://www.slac.stanford.edu/~strauman/rtems/ssrlapps-R_libbspExt_1_6.tgz && \
  tar xzvf ssrlapps-R_libbspExt_1_6.tgz && \
  cd /gem_base/targetOS/RTEMS/source/rtems/ssrlapps-R_libbspExt_1_6 && \
  mkdir build && \
  cd build && \
  ../configure --with-rtems-top=/gem_base/targetOS/RTEMS/rtems-4.10 --prefix=/gem_base/targetOS/RTEMS/rtems-4.10 --with-package-subdir=. --enable-std-rtems-installdirs && \
  make && \
  make install
 
 
 # install tito
 RUN yum -y install tito
 RUN yum clean all
 
 # create gem-rtsw.repo: This is a hack for now, ww should create an rpm for that
 RUN echo "[gem-rtsw]" > /etc/yum.repos.d/gem-rtsw.repo && \
  echo "name=Gemini RTSW group software packages" >> /etc/yum.repos.d/gem-rtsw.repo && \
  echo "baseurl=http://hbfswgrepo-lv1.hi.gemini.edu/repo/gembase/" >> /etc/yum.repos.d/gem-rtsw.repo && \
  echo "gpgcheck=0" >> /etc/yum.repos.d/gem-rtsw.repo && \
  echo "timeout=0" >> /etc/yum.repos.d/gem-rtsw.repo
 
 
 
 CMD ["/bin/bash"]
 
It contains a manual :code:`RTEMS` installation and the Gemini RPM repository information.

Building
********
Comparable to the :code:`docker` documentation mentioned above, this image can be built from the directory where :conde:`Containerfile` is located by

::

 sudo podman build -t centos8:RTEMS -f Containerfile



Create Container Registry
*************************
:code:`mock` needs to pull its containers from a registry. For our purpose, a private one hosted via podman itself will fit our
needs.

::
 
 sudo mkdir /var/lib/containers/registry
 sudo podman run --privileged -d --name registry -p 5000:5000 -v /var/lib/containers/registry:/var/lib/registry --restart=always registry:2
 sudo podman tag localhost/centos8:RTEMS localhost:5000/centos8:RTEMS
 sudo podman push localhost:5000/centos8:RTEMS

The outcome should look like

::

 $ ls /var/lib/containers/registry/docker/registry/v2/repositories/
 centos8


