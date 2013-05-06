OpenDaylight-Lab
================

This VM has preinstalled, controllers and OpenFlow tools on a VM image. Very little if any Linux knowledge is required.


The VM contains the following working components:  
==========================================
-A working OpenDaylight bootstrap controller.  
-Wireshark w/ OpenFlow dissector.  
-Eclipse w/ working OpenDaylight bootstrap controller.  
-VM running Ubuntu 12.04.  
-Intructions for installing all of the software above.  
-The only service running outside of the vanilla Ubuntu install is ssh daemon. To remove run "sudo apt-get purge openssh-server". 
-VM is set to attach to the bridge your Nic is attached to rather then sharing the hosts NIC (PAT).  
-DHCP is enabled on the VM.  

==========================================
Section I. Getting Started with OpenDaylight 
==========================================

Both ODP and Wireshark are preinstalled on the guest VM image. The second section has instructions to install the software on another host.  

Here is the video from the project http://youtu.be/2K6Wnt3eMb8  

Or you can clone the project to download it with:  
git clone https://github.com/nerdalert/OpenDaylight-Lab.git  

### Download the VM  ###

Thanks to my buddy Bill Owens of Nysernet.org for hosting the VM image. Bill also hosts some cool stuff from our friends at https://github.com/CPqD/   
ftp://anonymous@paperboy.nysernet.org  (Connect as "Guest" or Anonymous)  


I have moved it off GitHub since they disabled downloads and Dropbox likes to disable accounts for BW violations :-/  
https://filezilla-project.org  

### Vmware Image Import  ###

*The ODP-Lab1-VM.mp4.zip (40MB) Is a video to look at before you download to see if it is something you want to download.  
*The ODP-VM-Ops.zip (1.6GB) image contains both current bootstraps Wireshark + Dissector and intructions to start them both.  
*The ODP-VM-Dev.zip (2.7GB) image contains both bootstraps already built into Eclipse along with the controller, Wireshark + Dissector, both current bootstraps and intructions to start them both.  

The images are in a .OVF format that should interop with about any hypervisor out there. I am leaving the how to below for *.Vmwarevm in case anyone needs it but I have changed the images to *.OVF.  
Vmware (Commercial - Easy) - If using VMware add a guest host and locate the existing virtual disk or just unzip and double-click the vmwarevm image file.   

### VirtualBox Image Import  ###

VirtualBox (Free - Easyish) - Before importing the guest VM, you just need to convert the ODP-VM-Lab1.vmwarevm image into an OVF format to import into VirtualBox. 

Download VirtualBox at https://www.virtualbox.org/wiki/Downloads  

Download the VMware OVF Tool (Open Virtualization Format) http://www.vmware.com/support/developer/ovf/  
The tool is freeish other then having to register with Vmware or login   

ovftool original-disk.vmx new-disk.ovf  
e.g.  
ovftool ODP-VM-Lab1.vmx ODP-VM-Lab1.ovf  

Windows Example: ovftool “C:\Users\NAME\Documents\Virtual Machines\Windows 7 x64\ODP-VM-Lab1.vmx” C:\ODP-VM-Lab1.ovf  
Mac OSX Example: /Applications/"VMware Fusion.app"/Contents/Library/"VMware OVF Tool"/ovftool ODP-VM-Lab1.vmwarevm/ODP-VM-Lab1.vmx ./ODP-VM-Lab1.ovf  

After the conversion, delete the Manifest file created 'ODP-VM-Lab1.mf' to avoid a  (VERR_MANIFEST_FILE_MISMATCH) error on import.

Once converted, Open VirtualBox -> File -> Import Appliance -> Find ODP-VM-xBox.ovf (or whatever you exported it to).  
Adjust any of the resources (CPU, Mem, Nic) and click import.  

### KVM / Qemu Image Import  ###

KVM (Free -Advanced) - KVM has native support for VMDK images. Look at the VMX config file for the following attributes:    
scsi0:0.fileName, uuid.bios, ethernet0.generatedAddress  

example: kvm -drive file=zimbra-000001.vmdk,boot=on \  
  -net nic,macaddr=00:0c:29:c3:93:b9 -net tap \   
  -uuid 564d3f3d-3280-5bf2-9431-21c9b2c393b9   
or convert it with:  
kvm-img convert -O qcow2 zimbra-000001.vmdk zimbra.qcow2  

### VM System Settings ###

Default VM account UID/password  
User ID: odp  
password: passwd  
Change the root password type: "passwd" in the console.  
It is recommended to install the guest OS tools whether using VirtualBox or VM Fusion for mem ballooning copy/paste etc.  

After installing the mini.iso image, you can add a fairly stripped down Ubuntu GUI with the following (~500MB):  
sudo apt-get install xorg xterm gdm ubuntu-desktop menu firefox gksu synaptic --no-install-recommends  

==========================================

### Starting OpenDaylight (ODP) Bootstrap #1 ###

1. cd /home/odp/controller/opendaylight/distribution/opendaylight/target/distribution.opendaylight-0.1.0-SNAPSHOT-osgipackage/opendaylight  
2. ./run.sh  
3. Click hyperlink on the desktop or any browser that can reach the IP of the guest VM to http://<ip>:8080  
The OpenDaylight web UI is:  
Username: admin  
Passwork: admin  

==========================================

### Operating OpenDaylight SDN Controller Platform (OSCP) Bootstrap #2 ###

Start a copy of the Controller.  
make start-sdnplatform  
or   
java -jar target/sdnplatform.jar   

To stop the controller:  
make stop-sdnplatform  

==========================================

### Starting Wireshark ###


If you want to run everything on another machine, ssh to the guest VM using "ssh -X <ip address>" (upper case) to X11 forwarding.  

1. Double click the Wireshark icon on the desktop (run with sudo privs, ignore Lua error).  
2. Capture -> Interfaces -> eth0. Then start the capture.  
3. Type "of" (no parentheses) in the filter window to ignore all packets other then OpenFlow packets (recommended).  

==========================================

### Using ODP ###

Recommend using X11 forwarding to the mininet instance in order to forward Wireshark w/X11 over the SSH session.  "ssh -X <mininet ip address>"  
1. Start the Mininet or physical switch.  
2. If physical switch piont it at the running VMs IP addr.  
3. If Mininet, start the mininet VM and from the CLI type:  
sudo mn --controller=remote,ip=<ControllerIP Here> --topo tree,3  


The newest version of mininet has a different syntax:  
sudo mn --mac --controller=remote --<ControllerIP Here> --port=6633  

A video using Mininet w/ODP can be found here:  
http://networkstatic.net/opendaylight-openflow-tutorial/  

Follow this tutorial for configuring ODP Bootstrap #1.   
https://wiki.opendaylight.org/view/OpenDaylight_Controller:Installation   

Follow this tutorial for configuring ODP Bootstrap #2.   
https://wiki.opendaylight.org/view/OpenDaylight_SDN_Controller_Platform_(OSCP):Installation   

There will be more in coming weeks as functionality increases and projects merge.

==========================================
Section II. Installing the software on another machine
==========================================
The rest of this section if for installing the software that is on the VM image in this repo. If you are just using that image, you don't need to install any of this. Building out your own lab rig would be good experience for anyone unfamiliar with Linux or building software.  

### Installing a Hypervisor and Guest VM ###  

Setting up a stripped down Ubuntu VM. Download the Minimal CD.  
Ubuntu mini.iso - https://help.ubuntu.com/community/Installation/MinimalCD  
A video of installing with VirtualBox can be found here:   
http://networkstatic.net/openvswitch-and-openflow-lab-preparation/  

==========================================

### Installing OpenDaylight Controller Bootstrap #1 ###

This is in order to run the project.  

1. apt-get update   
2. apt-get install maven git openjdk-7-jre openjdk-7-jdk   
3. git clone http://git.opendaylight.org/gerrit/p/controller.git  
4. cd controller/opendaylight/distribution/opendaylight/  
5. mvn clean install  
6. cd target/distribution.opendaylight-0.1.0-SNAPSHOT-osgipackage/opendaylight  
7. export JAVA_HOME=/usr/lib/jvm/java-1.7.0-openjdk-i386/ (add to ~/.bashrc for ENV persistency)  
8. ./run.sh  

==========================================

### Installing OpenDaylight SDN Controller Platform (OSCP) Bootstrap #2 ###

sudo apt-get install unzip python-dev python-virtualenv build-essential ant git openjdk-7-jdk  
git clone http://git.opendaylight.org/gerrit/p/net-virt-platform.git  
cd net-virt-platform/  
./setup.sh  
make  

Prepare the CLI w/  
source /home/odp/net-virt-platform/workspace/ve/bin/activate  
make stop-sdncon reset-cassandra start-sdncon  

==========================================

### Installing Wireshark and the OpenFlow dissector ###

1. apt-get update && apt-get install wireshark-dev wireshark mercurial git scons  
2. hg clone https://bitbucket.org/barnstorm/of-dissector  
3. cd of-dissector/src  
4. alias sudo='sudo env WIRESHARK=/usr/include/wireshark/'  
5. sudo scons install  
#This creates a shared object (so) named openflow.so.  
#Move openflow.so to the Wireshark plugin directory.  
6. sudo mv /home/odp/.wireshark/plugins/openflow.so /usr/lib/wireshark/libwireshark2/plugins/  
7. Start Wireshark, look in Help->About->Plugins tab. You should see openflow.so as a listed dissector.  

==========================================

### Installing Eclipse Bootstrap #1 ###

Architectural overview of ODP https://wiki.opendaylight.org/view/OpenDaylight_Controller:Architectural_Principles  

Download Eclipse IDE for Java EE Developers:  
http://www.eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/junosr2  
tar xvfz eclipse-jee-juno-SR2-linux-gtk-x86_64.tar.gz  

Instructions for importing Bootsrap #1 into Eclipse see:  
https://wiki.opendaylight.org/view/OpenDaylight_Controller:Eclipse_CLI_Setup  

Video for importing ODP Bootstrap #1 into Eclipse:  
https://wiki.opendaylight.org/view/OpenDaylight_Controller:Importing_OpenDaylight_Controller_into_Eclipse   


For a Pull only Eclipse build the following will get you started.  
1. git clone http://git.opendaylight.org/gerrit/p/controller.git  
2. cd controller/opendaylight/distribution/opendaylight/    
3. From the CLI run -> mvn clean install (5-10 minutes and requires decent bandwidth. It is slightly sensitve to lame bandwidth like my DSL.)  
3. Open Eclipse and install m2e.   
2. Help -> Install software -> "work with" enter the URI http://download.eclipse.org/technology/m2e/releases  
3. Choose the latest m2e release v1.3x. next, next, yes, finish, restart, etc.  
4. Import ODP: File -> import -> Maven -> Existing Maven Project -> Find the ODP dir. Anywhere at the top of the git clone  
5. Eclipse will parse out the pom.xml files and present all builds (leave everything checked). There should be 0 errors at this point.  
6. Click finish. Eclipse will install Tycho and buildhelper automatically. Next, next, finish, except warning of non-signed SW. Eclipse will ask to restart again.  
7. If a new install you will get the welcome screen again, just close that. Next Maven will reconcile the project, watch the status bar in the bottom right. Avoid too much clicking around until it is done to avoid any issues.  
8. After the bottom right status gets to 100%. In the left window, drill down into distribution.opendaylight. Find opendaylight.assembleit.launch, right click on it -> run-as -> 1. assembleit.launch. Maven will again build the project for a few minutes. You will likely have some errors in the bottom window and can likely ignore them.  
9. If successful, the console will eventually return "[INFO] Build Seccesful".  
10. You can now run the controller directly from Eclipse by right clicking on "opendaylight-application.launch" -> run-as -> oepndaylight-application. To stop the controller click the red square red stop button.  

### Importing Bootstrap #2 into Eclipse ###

Bootstrap #2 Overview:  
https://wiki.opendaylight.org/view/OpenDaylight_SDN_Controller_Platform_(OSCP):Main#Open_SDN_Architecture

Instructions for importing Bootsrap #2 into Eclipse see:  
https://wiki.opendaylight.org/view/OpenDaylight_SDN_Controller_Platform_(OSCP):Installation  

1. make eclipse  
2. Import "sdnplatform" project into any eclipse workspace  

### Installing Open vSwitch ###

OVS can be used much like mininet for integrating hosts.  

1. apt-get install openvswitch-datapath-source bridge-utils  
2. module-assistant auto-install openvswitch-datapath  
3. apt-get install openvswitch-brcompat openvswitch-common openvswitch-controller  

==========================================

If you have the cycles, come contribute to the project. You dont have to be a programmer to contribute. Lots of documentation and architectural frameworks need to be created so jump in! Even just learning about OpenFlow / SDN and sharing that with someone else is a contribution. Hopefully this can cut down on basic build problems to keep dev focused. I will update as ODP milestones make sense. Feel free to contact me on twitter @networkstatic for information and be sure to join the mailing lists to share and collaborate with others using SDN.   

Regards,  
Brent  
@networkstatic  
http://networkstatic.net  

http://networkstatic.net/pre-built-opendaylight-vm-images/
