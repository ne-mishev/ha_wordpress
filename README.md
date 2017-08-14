# ha_hello_world

Automatic deployment of a HA SSL (with Let'sEncrypt certificate) web application with Vagrant boxes and Ansible provisioning

DEMO: http(s)://hello-world.hopto.org/

Prerequisites (non-automated tasks):
1. You must register/own the desired domain name
2. Make sure it is publicly resolvable
3. Make sure the haproxy host will be accessible from the internet on port 80 during provisioning*

All three steps are required for successful SSL certificate creation. 

*Let'sEncrypt challenges certificates by bringing up a web server listening on port 80

If you are going to run this on a Linux physical box/VM you can use the "prerequisites" playbook to make sure you have everyting needed to bring up the vagrant boxes and configure them. To run the playbook, simply execute:

[anon@centosbox ha_hello_world]$ git clone git@github.com:ne-mishev/ha_hello_world.git

[anon@centosbox ha_hello_world]$ cd ha_hello_world/

[anon@centosbox ha_hello_world]$ ansible-playbook -i "localhost," -c local prerequisites/prep.yml

"Prerequisite" playbook has been testeon on CentOS 7 and Debian 9. Naturally you need to have git and ansible installed before running it.

Once prerequisites are done, you need to change some variables and you are good to go:
1. Edit provisioning/group_vars/all file with the desired IPs and Hostnames. This is not mandatory, the provisioning will work with the current values.
2. Edit provisioning/roles/haproxy/defaults/main.yml to set the desired domain name under:
 - letsencrypt_certs: populate the list under domains
 - letsencrypt_default_domain:

Now we are all good. Just run:

[anon@centosbox ha_hello_world]$ vagrant up

This will bring up the boxes and provision them.
When finished you should be able to access your brand new and secure web app on https://{{ letsencrypt_default_domain }}/
