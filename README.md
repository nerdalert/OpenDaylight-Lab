OpenDaylight-Lab
================

OpenDaylight SDN / OpenFlow controller Lab VM Image including Wireshark with an OpenFlow dissector

Download VirtualBox at https://www.virtualbox.org/wiki/Downloads

The VM contains the following working components:

-A working OpenDaylight bootstrap controller.
-Wireshark w/ OpenFlow dissector.
-Eclipse w/ working OpenDaylight bootstrap controller.
-VM running Ubuntu 12.04.
-Intructions for installing all of the software above.

==========================================
Section I. OpenDaylight and Wireshark
==========================================

Both ODP and Wireshark are preinstalled on the guest VM image. The second section has instructions to install the software on another host.

### VM System Settings ###

Default VM account UID/password
User ID: odp
password: passwd
Change the root password type: "passwd" in the console.
It is recommended to install the guest OS tools whether using VirtualBox or VM Fusion.
After installing the mini.iso image, you can add a fairly stripped down Ubuntu GUI with the following (~500MB):
sudo apt-get install xorg xterm gdm ubuntu-desktop menu firefox gksu synaptic --no-install-recommends

==========================================

### Starting OpenDaylight (ODP) ###

1. cd /home/odp/controller/opendaylight/distribution/opendaylight/target/distribution.opendaylight-0.1.0-SNAPSHOT-osgipackage/opendaylight 
2. ./run.sh
3. Click hyperlink on the desktop or any browser that can reach the IP of the guest VM to http://<ip>:8080
The OpenDaylight web UI is:
Username: admin
Passwork: admin

==========================================

### Starting Wireshark ###

/*
If you want to run everything on another machine, ssh to the guest VM using "ssh -X <ip address>" (upper case) to X11 forwarding.

1. Double click the Wireshark icon on the desktop (run with sudo privs, ignore Lua error).
2. Capture -> Interfaces -> eth0. Then start the capture. 
3. Type "of" (no parentheses) in the filter window to ignore all packets other then OpenFlow packets (recommended).

==========================================

### Using ODP ###

/*
Recommend using X11 forwarding to the mininet instance in order to forward Wireshark w/X11 over the SSH session.  "ssh -X <mininet ip address>"
1. Start the Mininet or physical switch.
2. If physical switch piont it at the running VMs IP addr.
3. If Mininet, start the mininet VM and from the CLI type:
 sudo mn --controller=remote,ip=172.16.102.161 --topo tree,3

Follow this tutorial for configuring ODP. There will be more in coming weeks as functionality increases.
https://wiki.opendaylight.org/view/OpenDaylight_Controller:Installation

==========================================
Section II. Installing the software on another machine
==========================================

### Installing a Hypervisor and Guest VM ###

Setting up a stripped down Ubuntu VM. Download the Minimal CD.
Ubuntu mini.iso - https://help.ubuntu.com/community/Installation/MinimalCD
A video of installing with VirtualBox can be found here: 
http://networkstatic.net/openvswitch-and-openflow-lab-preparation/

==========================================

### Installing OpenDaylight ###

1. apt-get update
2. apt-get install maven git openjdk-7-jre openjdk-7-jdk 
3. git clone http://git.opendaylight.org/gerrit/p/controller.git
4. cd controller/opendaylight/distribution/opendaylight/
5. mvn clean install
6. cd target/distribution.opendaylight-0.1.0-SNAPSHOT-osgipackage/opendaylight
7. ./run.sh

==========================================

### Installing Wireshark and the OpenFlow dissector ###

1. apt-get update && apt-get install wireshark-dev wireshark mercurial git scons
2. hg clone https://bitbucket.org/barnstorm/of-dissector
3. cd of-dissector/src
4. alias sudo='sudo env WIRESHARK=/usr/include/wireshark/'
5. sudo scons install
/*
#This creates a shared object (so) named openflow.so.
#Move openflow.so to the Wireshark plugin directory.
*/
6. sudo mv /home/odp/.wireshark/plugins/openflow.so /usr/lib/wireshark/libwireshark2/plugins/
7. Start Wireshark, look in Help->About->Plugins tab. You should see openflow.so as a listed dissector.

==========================================

### Installing Eclipse ###

Download Eclipse IDE for Java EE Developers:
http://www.eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/junosr2
tar xvfz eclipse-jee-juno-SR2-linux-gtk-x86_64.tar.gz

Instructions for importing this bootsrap into Eclipse see:
https://wiki.opendaylight.org/view/OpenDaylight_Controller:Eclipse_CLI_Setup

Video for importing ODP into Eclipse:
https://wiki.opendaylight.org/view/OpenDaylight_Controller:Importing_OpenDaylight_Controller_into_Eclipse


### Installing Open vSwitch -  ###
OVS can be used much like mininet for integrating hosts

1. apt-get install openvswitch-datapath-source bridge-utils
2. module-assistant auto-install openvswitch-datapath
3. apt-get install openvswitch-brcompat openvswitch-common openvswitch-controller

==========================================

If you have the cycles, come contribute to the project. You dont have to be a programmer to contribute. Lots of documentation and architectural frameworks need to be created so jump in! Feel free to contact me on twitter @networkstatic for information and be sure to join the mainling lists to share and collaborate with others using SDN.

Regards,

Brent

@networkstatic

==========================================
