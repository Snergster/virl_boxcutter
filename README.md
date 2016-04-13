THIS DOCUMENT IS FOR USERS WHO WANT TO RUN 'VIRL on PACKET' DIRECTLY FROM THEIR WORKSTATION/LAPTOP. YOU MUST HAVE A VALID VIRL LICENSE KEY. IF YOU WISH TO RUN 'VIRL on PACKET' FROM YOUR VIRL SERVER, PLEASE SEE 'https://github.com/Snergster/virl_packet/blob/master/salt.README.md'

Virl_boxcutter is a self-contained 'launcher' virtual machine image, used to bring up a VIRL Server on the Packet.net platform. You do NOT need to have VIRL installed on your workstation or laptio. The launcher uses Vagrant (from Hashicorp) to control the operation of the 'launcher VM'. 

Vagrant offer free plugin support for Virtualbox and for Vmware AppCatalyst. Other plugins are provided at a cost.

#Steps:
1. register for a Packet.net account
  1. Create api key token

2. Install either Virtualbox (https://www.virtualbox.org/wiki/Downloads) for Windows, Mac or Linux, or Vmware AppCatalyst (https://www.vmware.com/cloudnative/appcatalyst-download) for Mac.

3. Install Vagrant (https://www.vagrantup.com/downloads.html) for Mac or Linux. 

   WINDOWS USERS - please install Vagrant 1.7.4 from https://releases.hashicorp.com/vagrant/1.7.4/vagrant_1.7.4.msi. DO NOT INSTALL 1.8.x

4. You must ensure that you have installed the appropriate Vagrant plugin, using the command:

   `vagrant plugin install virtualbox`
   or
   `vagrant plugin install vagrant-vmware-appcatalyst`

5. Install a Git client of your choice then 'clone' the repo at `https://github.com/Snergster/virl_boxcutter`

6. The virl_boxcutter directory will be created once the clone operation completes.

7. In the 'virl_boxcutter\salt' directory, copy the content of your VIRL license key (e.g. 'xxxxxxxx.virl.info.pem') into a file that MUST be called 'minion.pem'. MAKE SURE THAT YOU INCLUDE THE HEADER AND FOOTER OF THE FILE. 

   WINDOWS USERS - make sure that the file is NOT called 'minion.pem.txt'!!!

8. In the 'virl_boxcutter' directory, make a copy of the file 'id.conf.orig' and call it 'id.conf'.

9. Edit the 'id.conf' file. The 'id' field must be set to be the value 'xxxxxxxx' from your VIRL license key ('xxxxxxxx.virl.info.pem'). The 'append_domain' field must be set to the domain field from your VIRL license key. If your domain field is 'virl.info', then this should be put in the 'append_domain' field. If your domain field is 'virl30.info', then this should be put in the 'append_domain' field. Save the changes.

10. Make a copy of the 'settings.tf.orig' and call it 'settings.tf'.

11. Edit the 'settings.tf' file. Replace the packet_api 'default' field with your packet_api_key. You can also adjust the 'dead_mans_timer' value and the 'packet_machine_type' that will be used with the VIRL server is created. In addition, you can select where you want your VIRL server to be hosted from the available Packet.net data centers. EWR1 == New York, SJC1 == San Jose, CA, AMS1 == Amsterdam. Instructions in the settings.tf file will guide you to the changes that you need to make.

12. From a command window, go into the 'virl_boxcutter' directory and run the following command:

   `vagrant up`

   The system will now download the 'virl_boxcutter' VM and start the VM up. This will take some time to complete on its first download.
   
13. Once complete, use the command below to connect into the 'virl_boxcutter' VM

   `vagrant ssh`
   
   WINDOWS USERS - you must have an SSH client installed. Information from Vagrant will provide directions on how to connect to the 'virl_boxcutter' VM from your SSH client. If you are prompted for a password, the password is 'vagrant'.
   
14. Go into the 'virl_packet' directory using the command:

   `cd virl_packet`

15. Edit the file 'password.tf' to adjust the passwords to suit your needs (stick to numbers and letters for now please)

16. Review the 'Disclaimer.txt' file for operating rules. 

17. Before bringing up your VIRL server, log into to app.packet.net and click 'Manage'. You need to ensure that there are no active projects present. If there are active projects:

    1. Click on the name of your project listed
    2. Select "Settings" tab
    3. Scroll to the bottom of the page and click on the button to delete the project 


18. Run the command 

   `terraform plan .`
   
   This will validate the terraform .tf file.
   
19. Run the command 

   `terraform apply .`     
   
   This will spin up your remote VIRL server on Packet.net and install the VIRL software stack. If this runs without errors, expect it to take ~30 minutes. When it completes, the system will report the IP address of your Remote VIRL server. Login using
   
    `ssh root@<ip address>` or `ssh virl@<ip address>`
    
   NOTE - the VIRL server will reboot once the VIRL software has been installed. You must therefore wait until the reboot has completed before logging in.

20. To see more information about your Remote VIRL server, run the command 

   `terraform show` 
   
   The output will provided details of your Remote VIRL server instance.

21. The VIRL server is provisioned in a secure manner. To access the server, you must establish an OpenVPN tunnel to the server.
    1. Install an OpenVPN compliant client for your system.
    2. The set up of the remote VIRL server will automatically configure the OpenVPN server. The 'client.ovpn' connection profile will be automatically downloaded to the directory on the 'virl_boxcutter' VM from where you ran the `terraform apply .` command. 
    3. The 'client.ovpn' file can be copied out to other devices, such as a laptop hosting your local VIRL instance. You can copy the file to the '/vagrant' directory using the command `cp client.ovpn /vagrant/`. The file is then available on your laptop in the directory from which you issued the command 'vagrant up'.
    4. Open the 'client.ovpn' file with your OpenVPN client to bring up the tunnel to your VIRL server.

    NOTE - the VIRL server will reboot once the VIRL software has been installed. You must therefore wait until the reboot has completed before bringing up the OpenVPN tunnel.
    
22. With your OpenVPN tunnel up from your workstation or laptop, the VIRL server is available at http://172.16.11.254.
    If using VM Maestro, you must set up the connection profile to point to `172.16.11.254`

23. When you're ready to terminate your remote VIRL server instance, on your 'virl_boxcutter' VM, issue the command 
 
    `terraform destroy .`

    Ensure that all projects are successfully terminated by logging into to app.packet.net and click 'Manage'. If there are active projects:

    1. Click on the name of your project listed
    2. Select "Settings" tab
    3. Scroll to the bottom of the page and click on the button to delete the project 

24. To terminate your 'virl_boxcutter' VM type the commands:

   `exit'
   `vagrant halt`

25. Log in to the Packet.net portal
   1. Review the 'Manage' tab to confirm that the server instance has indeed been deleted and if necessary, delete the server
   2. Review the 'SSH Keys' tab and remove any ssh keys that are registered

To start up again, repeat step 12.

[NOTE] Your uwmadmin and guest passwords are in passwords.tf. If you can't remember them, this is where you can find them, or by running the command `terraform show`.

# To obtain your VM Maestro clients...
Once your VIRL Server has come up, log in to the UWM interface as 'uwmadmin' using your password. Navigate to the 'VIRL Server/VIRL Software' tab and select the VM Maestro client package(s) that you'd like. Now press 'install'. The package will be installed on your VIRL server and will be available from `http://172.16.11.254/download/`.

# If your VIRL server bring-up fails to complete successfully:

1. Terminate the instance using the command:

   `terraform destroy .`

2. Log in to the Packet.net portal
   1. Review the 'Manage' tab to confirm that the server instance has indeed been deleted and if necessary, delete the server.
   2. Review the 'SSH Keys' tab and remove any ssh keys that are registered
   3. Delete any project that is does not have an active server instance in it. Double-click on the project name and use the 'settings' panel to delete the project.
    
   [NOTE] a server can only be terminated on the Packet.net portal once the server's status is reported as 'green'. You may therefore need to wait for a few minutes in order for the server to reach this state.

# Dead man's timer:

When a VIRL server is initialised, a 'dead man's timer' value is set. The purpose of the timer is to avoid a server instance being left running on the platform for an indefinite period. 

The timer value is set by default to four (4) hours and can be changed by modifying the 'dead mans timer' value in the settings.tf file before you start your server instance. The value you set will be applied each time you start up a server instance until you next modify the value.

If your server is running at the point where the timer expires, your server instance will be terminated automatically. Any information held on the server will be lost.

You are able to see when the timer will expire by logging in (via ssh) to the server instance and issuing the command `sudo atq`. You can remove the timer, leaving the server to run indefinitely, by issuing the command `sudo atrm 1`.
