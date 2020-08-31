Using CentOS8 in VirtualBox
======================================
`VirtualBox <https://www.virtualbox.org/>`_ is a virtualization product to emulate a whole PC. Within this, a operating system like *CentOS-8* can be installed
like on a 'normal' computer. *VirtualBox* is available for Windows-, MacOS- and Linux-based host operating systems.

Installation of Guest Operating System CentOS-8
-----------------------------------------------
CentOS-8 (as well as other operating systems) is installed by 'virtually' inserting the iso-image downloaded as a virtual CD into the virtual drive. This
is done in the *storage* settings of the virtual machine, which has beed created before. Virtual Hard Disk space of about 30GB should be sufficient. Always make
sure to use the current version of *VirtualBox*.

Network Connection
------------------
There do exist two methods to activate VPN connection to Gemini, the second one is the recommended one since it is considered more performant:

Connecting through the host's OS' VPN connection
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
In the VM's network settings activate *NAT* and in the *Advanced* section set the adapter type to *Paravirtualized Network (virtio-net)*. Then VPN connection to Gemini can be activated on the host system. Reconnect your virtual network and the host system's VPN connection should be accessible from within the guest system now.

Connecting though OpenConnect-Compatible VPN connection
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
* Before booting the VM, configure the Network to be *NAT* or *Bridged*, but not *paravirtualized*. The NIC should be configured as normal Hardware like *Intel PRO/1000 MT Desktop*
* After booting CentOS 8, make sure you log in with the default *Standard (Wayland display server)* which is *Gnome*-based or *Plasma* 
* Install OpenConnect: :code:`sudo dnf install NetworkManager-openconnect NetworkManager-openconnect-gnome -y` for *Gnome* and/or :code:`sudo dnf install NetworkManager-openconnect plasma-nm-openconnect -y` for *Plasma*
* Reboot
* Log in again with the *Standard* display server or *Plasma*
* Configure a new VPN connection, where you only specify the *Protocol* to be *Cisco AnyConnect* and the *Gateway* to be :code:`hbfvpn2.gemini.edu`
* Another reboot may be needed
* Activate the VPN connection just created. Under *Plasma*, make sure to press the button with the *connect* symbol near the gateway URL in the popup dialog showing up. You will be asked for your login credentials which is the user's Gemini LDAP credentials in the :code:`STAFF` group. 

.. _testing RPM repository:


Building and Releasing RPMs from git
====================================

It is assumed that the following takes place on a *CentOS 8* machine. To set up one in a virtual environment, see `Using CentOS8 in VirtualBox`_.

Gemini's RTSWG RPM Repository
-----------------------------
First, id needs to be made sure that :code:`epel` and :code:`powertools` RPM repositories are installed and enabled:

::

  sudo dnf -y install dnf-plugins-core
  sudo dnf -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
  sudo dnf config-manager --set-enabled PowerTools
  
Having activated the VPN connection to or being located within HBF facility, you can ouput a list of the repository by opening its `URL <http://hbfswgrepo-lv1.hi.gemini.edu/repo/gembase/>`_ in a web browser. Copy the URL of the package beginning with :code:`gem-rtsw-repo` (usually somthing like *rightclick -> copy link location*) and install this repo in your virtual machine, e.g.

::

  sudo dnf install -y http://hbfswgrepo-lv1.hi.gemini.edu/repo/gembase/gem-rtsw-repo-3.15.8-0.1.0.20200731.git.0.9602532.el8.x86_64.rpm 
  
Please note that the repository is disabled by default (because it is only reachable within Gemini or being connected by VPN). To do any operations on
this repository, always use the :code:`--enablerepo=gem-rtsw` option. Or enable it permanently with

::

  sudo dnf install dnf-utils -y
  sudo dnf config-manager --enable gem-rtsw
  
Then :code:`gem-rtsw` packages can be installed, for example

::

  sudo dnf install epics-base-devel
  
or, for casrotator development packages

.. _dependencies:

::

  sudo dnf install crcs_mk-devel
  
which should install everything needed for casrotator development including dependecies like tdct, epics-base-devel etc. However, to be able to do development, sources would have to be checked out from Gemini's gitlab repository (see ).
  
To list all packages available in :code:`gem-rtsw` execute

::

  dnf --disablerepo=* --enablerepo=gem-rtsw list available
  
If the RPM repository was updated recently (e.g. with a RPM that has just been deployed) it is a good idea to update to update the local cache of repository information:

::

  sudo dnf makecache
  
A complete command reference for :code:`dnf` can be found `here <https://dnf.readthedocs.io/en/latest/command_ref.html>`_.

Gemini's gitlab Repository
---------------------------
The group :code:`rtsw` within Gemini's gitlab repository is located at `https://gitlab.gemini.edu/rtsw <https://gitlab.gemini.edu/rtsw>`_. Geminites should be able to login with their LDAP credentials.

The :code:`crcs_mk` ioc module is located in the :code:`ioc` subgroup. All dependencies are locatet in the :code:`support` subgroup.

.. _`ssh public key`:

Clone Project Using SSH Public Key
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
To be able to clone a gitlab project with a SSH key, it first has to be made sure one exists on your local CentOS8 virtual machine. Open a terminal and 

::

  cat ~/.ssh/id_rsa.pub
  
If this produces an error message, a key pair needs to be generated by

::

  ssh-keygen -t rsa

For comfortablity, it is recommended to :code:`enter` through the whole process, no password, just standards. Afterwards, the public key should be :code:`cat` like above.

Then the user needs to logon to gitlab, navigate to *Settings->SSH Keys* and add the key following the given procedure by copy-pasting the key that just was :code:`cat` above. 

It is now possible to do

.. _clone:

::

  git clone git@gitlab.gemini.edu:rtsw/support/<project name>.git
  
or

::

  git clone git@gitlab.gemini.edu:rtsw/ioc/<project name>.git
  
and afterwards any other git operation on those projects without having to enter user credentials.


Set upstream for vendor modules
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Introduction
""""""""""""
A bunch of *EPICS* modules is managed on `github <https://github.com/epics-modules>`_. These can be set to be *upstream* by adding their URL to the respective project's git configuration. This way it is always possible to merge the newest changes from *upstream* into Gemini's sources to be up to date. Please read `this <https://www.atlassian.com/git/tutorials/git-forks-and-upstreams>`_ for a short and good overview how things work regarding this.

In our setup we might have unrelated hostories of development. This means that an appropriate flag needs to be set when merging from upstream:

::

  git merge --allow-unrelated-histories upstream/master

Merging would lead to conflicts which would have to be resolved manually. In some cases (like :code:`adl` files) this might me very straight forward and it's safe to use upstream's version, which cold be achieved by:

::

  git checkout --theirs <path/to/file>
  
In all other cases it's mandatory to resolve the conflict manually by opening the respective file(s) in your favorite editor and look for lines characterized by:

::
  
  <<<<<<< HEAD
  <your stuff here>
  =======
  <upstream's stuff here>
  >>>>>>> upstream/master

Example Workflow
""""""""""""""""
Putting all together, a example workflow for the *EPICS* module :code:`autosave` to merge upstream sources into the existing git repo is depicted in the
following. 

* First, make sure local :code:`master` is up to date:

  ::

    git checkout master
    git pull
  
* check for exisiting remotes:
  
  ::
  
    $ git remote -v
    origin  git@gitlab.gemini.edu:rtsw/support/autosave.git (fetch)
    origin  git@gitlab.gemini.edu:rtsw/support/autosave.git (push)

* add the upstream (i.e. vendor) URL and check that everything worked well:

  ::
  
    $ git remote add upstream https://github.com/epics-modules/autosave
    $ git remote -v
    origin  git@gitlab.gemini.edu:rtsw/support/autosave.git (fetch)
    origin  git@gitlab.gemini.edu:rtsw/support/autosave.git (push)
    upstream        https://github.com/epics-modules/autosave (fetch)
    upstream        https://github.com/epics-modules/autosave (push)

* branch off from master to a new working branch:

  ::
  
    git checkout -b vendor-code
    
* fetch the latest changes from upstream

  ::
  
    git fetch upstream
    
* now upstream's master needs to be merged into the branch just created:

  ::
  
    git merge upstream/master
    
  If this results in an error message :code:`fatal: refusing to merge unrelated histories` the flag mentioned above needs to be set (and some conflicts forseen
  
  ::
    
    $ git merge --allow-unrelated-histories upstream/master
    Auto-merging documentation/autosaveReleaseNotes.html
    CONFLICT (add/add): Merge conflict in documentation/autosaveReleaseNotes.html
    Auto-merging configure/RELEASE
    CONFLICT (add/add): Merge conflict in configure/RELEASE
    Auto-merging asApp/src/save_restore.c
    CONFLICT (add/add): Merge conflict in asApp/src/save_restore.c
    Auto-merging asApp/src/dbrestore.c
    CONFLICT (add/add): Merge conflict in asApp/src/dbrestore.c
    [ ... many more ... ]
    
* Now the work begins and all conflict need to be resolved manually or with the :code:`--theirs` or :code:`--ours` option to :code:`git checkout <filename>`, 
   but only if absolutely certain which version to take
   
* If the conflicts where resolved, commit the changes by :code:`git commit -a`
 
* To tag the respective branch for a new tito release :code:`tito tag` needs to be called followed by :code:`git push -u --follow-tags origin vendor-code`
 
* To try things out a RPM for a testing repo (and only for this one) could be deployed by 
 
  ::
    
    RSYNC_USERNAME=koji tito release gemrtsw-el8-x86_64
    
  It is advised to rebuild RPMs for all packages having this one (:code:`autosave` in this case) as dependency in this testing repo
    
* If everything work well file a merge request for the branch :code:`vendor-code` to be merged into :code:`master`.

Using tito to Build and Deploy RPMs
-----------------------------------
In Gemini's test environment :code:`tito` (documentation to be found `here <https://github.com/rpm-software-management/tito>`_) is used to build and deploy RPMs to the `testing RPM repository`_. It can be installed implicitly (together with Gemini-specific config files) by

::

  sudo dnf install -y gemini-ade
  
in the CentOS8 VM. This package is also a dependecy of :code:`epics-base-devel` and all other devel packages for epics modules from Gemini's RPM repository.

The typical workflow is to 
  * clone_ a project, 
  * enter its directory and do some changes, 
  * test to build while hopefully all dependencies_ are installed using the typical command set (for *EPICS* projects usually something like :code:`make distclean uninstall all`), 
  * :code:`git commit -a` those changes and 
  * :code:`tito tag` them. 
  * Then those changes could be released as *RPM* to the repository doing :code:`RSYNC_USERNAME=koji tito release gemrtsw-el8-x86_64`
  
.. note:: The public ssh key (usually :code:`~/ssh.id_rsa.pub`, see `ssh public key`_) has to be added to the :code:`authorized_keys` of the :code:`koji` user at Gemini's RPM repository machine. Please post your public key to Matt at gemini-software.slack.com with a request to be added to those.

  
