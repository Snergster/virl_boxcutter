Placeholder README

Need Virtualbox, Vagrant (and virtualbox plugin), Git tool, Internet access

1. install virtualbox  https://www.virtualbox.org
2. install vagrant  https://www.vagrantup.com
2. install git 
3. clone this repo
4. install purchased key as minion.pem in salt directory
5. copy id.conf.orig to id.conf
5. edit id.conf file to fill in salt id and domain provided
6. cp settings.tf.orig to settings.tf
7. edit settings.tf to supply packet api key
5. vagrant up
6. vagrant ssh
6. cd into virl_packet or virl_cluster as desired
7. terraform apply .
8. refer to virl_packet or virl_cluster instructions from this point
