
----![Ignite](https://github.com/datacenter/ignite/blob/master/dist/images/color-logo.png)

# Description

Ignite is a tool to bootstrap your network. It supports Cisco Nexus switches leveraging Power-On Auto Provisioning (POAP) capabilities.

Ignite provides bootstrapping with the following capabilities:
* Topology design
* Configuration design
* Image and configuration store for POAP
* POAP request handler
* AutoNetkit integration for automatic generation of configurations - see docs folder in Ignite github for more details

Ignite is integrated with AutoNetkit.  Autonetkit is a network configuration tool, which automatically generates network configurations from a topology. It extracts the topology information and builds configurations required to enable BGP, OSPF protocols between network elements.  It also has the ability to allocate IP addresses required for such configurations.  It is a highly flexible and configurable tool for building network configurations for mutli-device, multi-vendor networks.

##### Acknowledgement to AutoNetkit Contributors

The following people have contributed to AutoNetkit:

###### Simon Knight, Hung Nguyen, Nickolas Falkner, Joel Obstfeld, Olaf Maennel, Iain Phillips, Askar Jaboldinov, Matthew Roughan.


Thanks also to Hitesh Mali, Ingmar Poese, Michael Rumsewicz, Randy Bush, Niklas Semmler, Stephen Steiner who worked on bugfixes, improvement and suggestions for AutoNetkit.  AutoNetkit project was supported by the Australian Government through an Australian Postgraduate Award, Australian Research Council Discovery Grants DP110103505 and DP0985063, and by the University of Adelaide.  For more details on AutoNetkit please visit http://autonetkit.org/

----![Ignite Screenshot](https://github.com/datacenter/ignite/blob/master/dist/images/ignite-screenshot.png)

# Getting Started

### Option 1: Download OVA from link below:
https://cisco.box.com/s/033ij1hnxteulcjzbaiu0n1c2at8ixjh

Username/password: ignite/ignite

Follow steps below

	a. Deploy OVA
	b. Login with ignite/ignite
	c. Change directory to ~/ignite/ignite (cd ~/ignite/ignite)
	d. Change to sudo (sudo su)
	e. Edit configure.sh to edit parameter values of following parameters
	   	ignite_ip :  IP address on which to run the server
	    ignite_port: port on which to run the server
	    vmusername: username for the server
	    vmpassword: password for the server
	f. Run configure.sh (sh configure.sh)
	g. Edit server_configuration.py and modify the following sections.
    	IP Address of Ignite server and port (same as in step (e))
        IGNITE_IP='127.0.0.1'
        IGNITE_PORT='8000'

        Ignite server access details (same as in step (e))
        VMUSERNAME='ignite'
        VMPASSWORD='ignite'
        
        default switch image name - software image name sent to the requesting swtich.
        This name is used only if image_profile.py does not containt a default_image profile.
        DEFAULT_SWITCH_IMAGE_NAME = 'n9000-dk9.6.1.2.I3.2.bin'
	h. Change current directory to the directory where manage,py file is present (sudo cd /home/vmignite/ignite). You may be prompted for a password.
	i. Run ignite server (sudo nohup python manage.py runserver <ip>:<port> & ) where IP is the address provided in ignite_ip and port is value provided in ignite_port. Note: To avoide receiving a sudo password prompt while running the server, please run a dummy command for example "sudo ls", before you enter the command to run ignite server.
	j. To launch UI use http://<ipaddress>:<port>/ui/index.html
	k. Use New User registration link in the UI to create a new user credential. Login to the page using this credential.
To run Ignite as an Apache service do the following:

	a. Do all the steps above from (a) - (g)
    b. Uncomment the following line in /etc/apache2/ports.conf and change 8888 to ignite_port
        #Listen 8888 -> Listen <ignite_port>
	c. Enable ignite service
        cd /etc/apache2/sites-available/
        sudo a2ensite ignite.conf

	d. In /home/ignite/ignite/server_configuration.py do the following changes:
        Comment the following line:
        PROJECT_DIR = / -> #PROJECT_DIR = /
        Uncomment the following line:
        #PROJECT_DIR = /../../ignite/ignite -> PROJECT_DIR = /../../ignite/ignite
	e. Restart Apache server
       service apache2 restart

### Option 2: Create a new Ignite VM/Server with code from git

1.  Install postgresql

		apt-get install postgresql-9.3 postgresql-common


2.  Set up database

	    psql  -U postgres
    	create database  ignite_db;
    	\q

3.  Edit configure.sh to edit parameter values of following parameters

	   	ignite_ip :  IP address on which to run the server
	    ignite_port: port on which to run the server
	    vmusername: username for the server
	    vmpassword: password for the server

4.  Run configure.sh

        sh configure.sh

5.  Edit server_configuration.py and modify the following sections.

    	IP Address of Ignite server and port (same as in step (e))
        IGNITE_IP='127.0.0.1'
        IGNITE_PORT='8000'

        Ignite server access details (same as in step (e))
        VMUSERNAME='ignite'
        VMPASSWORD='ignite'
        
        default switch image name - software image name sent to the requesting swtich.
        This name is used only if image_profile.py does not containt a default_image profile.
        DEFAULT_SWITCH_IMAGE_NAME = 'n9000-dk9.6.1.2.I3.2.bin'

6.  Create tables in database

    	python ~ignite/ignite/manage.py makemigrations
    	python ~ignite/ignite/manage.py migrate


7. Following changes may require root permission

    Edit /etc/rsyslog.conf and add the following lines.  
    This is required to save logs from switches during POAP.py script execution. 

            $template DynaFile,"/var/log/remote/ignite/system-%HOSTNAME%.log"
            *.* -?DynaFile

8. Change current directory to the directory where manage,py file is present (sudo cd /home/vmignite/ignite). You may be prompted for a password.

9. Run ignite server

        sudo nohup python manage.py runserver <ip>:<port> 
        where IP is the address provided in ignite_ip and port is value provided in ignite_port.

        Note: To avoide receiving a sudo password prompt while running the server, please run a dummy command for example "sudo ls", before you enter the command to run ignite server.

10. To launch UI use 

		http://<ipaddress>:<port>/ui/index.html

11. Use New User registration link in the UI to create a new user credential. Login to the page using this credential.

### Option 3: Create a new Ignite VM/Server with code from git and run it as service in Apache

1. Install postgresql

        apt-get install postgresql-9.3 postgresql-common


2. Set up database (use DATABASE_NAME as given in server_configuration.py DBNAME='ignitedb'

        psql  -U postgres
        create database  ignitedb;
        \q

3. Edit configure.sh to edit parameter values of following parameters

	   	ignite_ip :  IP address on which to run the server
	    ignite_port: port on which to run the server
	    vmusername: username for the server
	    vmpassword: password for the server

4. Edit server_configuration.py and modify the following sections.

    	IP Address of Ignite server and port (same as in step (e))
        IGNITE_IP='127.0.0.1'
        IGNITE_PORT='8000'
      
        Ignite server access details (same as in step (e))
        VMUSERNAME='ignite'
        VMPASSWORD='ignite'
        
        default switch image name - software image name sent to the requesting swtich.
        This name is used only if image_profile.py does not containt a default_image profile.
        DEFAULT_SWITCH_IMAGE_NAME = 'n9000-dk9.6.1.2.I3.2.bin'

        Edit project directory information for apache.  Project directory is the full path where "wsgi.py" file is located in Ignite application directory, with reference to apache2 directory - /etc/apache2.  For example, if "wsgi.py" is in directory, "/home/vmignite/ignite/ignite/ignite/", project directory should be set as "/../../home/vmignite/ignite/ignite/ignite/"
        
        PROJECT_DIR='/../../home/vmignite/ignite/ignite/ignite/'
        
        Edit log directory (this directory stores device logs during POAP.py script execution)
        REMOTE_SYSLOG_PATH = '/var/log/remote/ignite/system-'

5. Run Configure.sh

       sh configure.sh
    
6. Create tables in database

        python ~ignite/ignite/manage.py makemigrations
        python ~ignite/ignite/manage.py migrate


7. Following changes may require root permission

    Edit /etc/rsyslog.conf and add the following lines.  
    This is required to save logs from switches during POAP.py script execution. 

            $template DynaFile,"/var/log/remote/ignite/system-%HOSTNAME%.log"
            *.* -?DynaFile

8. Apache Server setup

    (a) Install apache2, set directory to apache2 and create ignite.conf file
    
        sudo su
        apt-get install apache2 libapache2-mod-wsgi libapache2-mod-python
        cd /etc/apache2
        touch sites-available/ignite.conf
    (b) edit ignite.conf file and enter the following
    
        <VirtualHost *:8888> #port on which server will listen
        <Directory /home/ignite/ignite/ignite > #path to ignite where wsgi.py is located
             <Files wsgi.py>
                Require all granted
            </Files>
        </Directory>
        
        WSGIDaemonProcess ignite #name of the process could be anything
        #python-path update to point to the location where manage.py is located
        #for now keep ank path as it is
        python-path=/home/ignite/ignite:/usr/local/lib/python2.7/dist-packages:/home/ignite/ignite/ank/autonetkit

        WSGIProcessGroup ignite
        #WSGIScriptAlias update 2nd argument to have full  path of file wsgi.py
        WSGIScriptAlias / /home/ignite/ignite/ignite/wsgi.py

        WSGIPassAuthorization On
        #DocumentRoot to point to location of manag.py
        DocumentRoot /home/ignite/ignite

        ErrorLog ${APACHE_LOG_DIR}/ignite_error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        </VirtualHost>
    (c ) Edit /etc/apache2/ports.conf
    
        Add the following line to add the ignite server application port number.  Port number here should same as that in step 3 or step 4.
        
        Listen 8888
    (d) Edit /etc/apache2/apache2.conf file to add following lines
    
        <Directory /home/ignite/ignite/ignite> #path to manage.py
        	Options FollowSymLinks
        	AllowOverride None
        	Require all granted
        </Directory>

9. Run ignite server as a service

        cd /etc/apache2/sites-available
        a2ensite ignite.conf
        service apache2 restart



10. To launch UI use 

		http://<ipaddress>:<port>/ui/index.html 
	ipaddress and port corresponds to ignite_ip and ignite_port defined in step 3.

11. Use New User registration link in the UI to create a new user credential. Login to the page using this credential.

12. To stop server

     sudo service apache2 stop
     
     Note:  To keep Apache running and just disable ignite server site do following:

13. To restart server

    sudo service apache2 restart
    
14. To disable ignite site without stopping Apache

        cd /etc/apache2/sites-available
        a2dissite ignite.conf
        Note: http port used for ignite server will not be released by Apache when site is disabled.
        If you want to also remove the ignite server http port, edit /etc/apache2/ports.conf to comment out the line "Listen 8888"
15. To enable, previously disabled ignite site

        cd /etc/apache2/sites-available
        a2ensite ignite.conf
 


### POAP script for ignite server
1. ignite_poap.py is sample POAP script for ignite server

2. Set the following variables(currenty set to a default value) in ignite_poap.py to reflect ignite server ip address and port

    	hostname                = "172.31.219.76"
    	port                	= "8001"

3. This script file should be placed on the script server TFTP directory by the name poap.py

4. Here script sever referes to the server configured as 'tftp-server-name' in dhcpd.conf on DHCP server.

### Image Profiles
Image profiles specify details of the software images available for download to the switch during POAP process.  Following atttributes define an image profile:

	 {
    "image_profile_name": "spine_image", 
    "image": "n9000-dk9.6.1.2.I3.2.bin",
    "imageserver_ip": "172.31.216.138",
    "username": "root",
    "password": "cisco123",
    "access_protocol": "scp"
	 }
  
     image_profile_name - uniquely identifies the profile
  
     image - filename where the image is stored
  
     imageserver_ip : server where the image is stored
  
     username - username to login to the image server
  
     password - password to login to the image server
  
     access_protocol - ftp/http/scp/tftp protocol used to transfer files from server
  
  Image profiles are stored in ignite/fabric/image_profile.py.  The profiles should be modified to match your image needs and environment.  New profiles can be added similar to the pre-defined ones.  spine_image, leaf_image and default_image are example profiles.  An image profile is applied to spine tier or leaf tier of the fabric using "Set Defaults" in "Fabrics" view.  Once applied, this image profile will be sent to the switch during the POAP boot process along with start up config details.  If image profile is not applied, profile named "default_image" is sent to the switch during POAP boot process.

### Issues

1.Topology can not be modified once the Fabric is instantiated with that topolog y.

2.Topology details can not be edited via fabric interface.

3.If topology is cloned, port numbering for any new Leaf/Spine will in correct.

4.In topology default pane, changing the default values will not take effect on the topology.

5.In topology, manual editing of port numbers is not validated. This may clash w ith port numbers generated automatically when new Leaf/Spine is added. User must take care to avoid duplicates.

6.For Spine Switches, both uplink and downlink ports are assigned from the same list (Eth1/1-Eth1/64).

7.Port numbering in topology will work correctly upto 64 Leaf additions. After t hat port numbers will be incorrect.  
# License

Copyright 2015 Cisco Systems, Inc.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
