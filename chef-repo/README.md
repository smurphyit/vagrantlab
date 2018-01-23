This configuration is used to setup a "vagrant home labs network".  Your workstation will then be able to upload cookbooks to the chef server.
You will then be able to deploy those cookbooks to the nodes.

# Pre-requisites:
================

* virtualbox already installed (https://www.virtualbox.org/wiki/Downloads)
* vagrant installed (https://www.vagrantup.com/downloads.html)

Once this repository has been cloned, startup the chef server with:

# vagrant up chef-server

Now you will need the private key for communication between your workstation and the chef server.  Download the private key from the chef
server with: (using the default password for vagrant)

# scp -P 2222 127.0.0.1:~/admin.pem chef-repo/.chef/ 
# cd chef-repo
# knife ssl check
# knife ssl fetch

The knife ssl fetch command will go out and download the SSL certificate from chef-server.  You will now be able to communicate with the chef
server using your workstation.  

This can be validated with:
# knife ssl check

You should get something back like:

Connecting to host chef-server.test:443
Successfully verified certificates from `chef-server.test'

# Start chef node(s)
===================

Next startup a node(s) with:

# vagrant up node1-ubuntu

This will bring the node online, and bootstrap it with chef.  


END

This concludes the vagrant home lab setup.  Now that you have a chef server and some nodes, you can test out deploying cookbooks to your nodes
after you upload them to your chef server.




