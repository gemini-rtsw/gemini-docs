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
Unfortunately, right now there doesn't exist a Cisco-VPN solution for CentOS-8 which works with Gemini's. NetworkManager-vpnc is apparently not yet built for
CentOS-8.
To work around this issue in the VM's network settings activate *NAT* and in the *Advanced* section set the aapter type to 
*Paravirtualized Network (virtio-net)*. Then VPN connection to Gemini can be activated on the host system. Reconnect you virtual network and the host system's
VPN connection should be accasible from within the guest system now.

