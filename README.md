# playbooks to automate HPE OneView

This is a set of ansible playbooks to provision bare-metal servers using HPE OneView and Red Hat Satellite.

# setup

one time:
~~~
git clone https://github.com/HewlettPackard/oneview-ansible.git /usr/share/ansible/
sudo pip install hpOneView
~~~

Modify group_vars/* to suit the target environment. Update group_vars/config.json to match the HPE OneView login information. Create an inventory file with the desired hostnames and IPs that will be statically provisioned by Satellite:

~~~
## required for provisoning
ocp-master1.hpecloud.test ansible_host=192.168.2.173 ilo_ip=192.168.2.136
ocp-master2.hpecloud.test ansible_host=192.168.2.174 ilo_ip=192.168.2.137
ocp-master3.hpecloud.test ansible_host=192.168.2.175 ilo_ip=192.168.2.138
ocp-infra0.hpecloud.test ansible_host=192.168.2.172 ilo_ip=192.168.2.135
ocp-infra1.hpecloud.test ansible_host=192.168.2.170 ilo_ip=192.168.2.133
ocp-infra2.hpecloud.test ansible_host=192.168.2.171 ilo_ip=192.168.2.134
ocp-cns1.hpecloud.test ansible_host=192.168.2.176 ilo_ip=192.168.2.140
ocp-cns2.hpecloud.test ansible_host=192.168.2.177 ilo_ip=192.168.2.141
ocp-cns3.hpecloud.test ansible_host=192.168.2.178 ilo_ip=192.168.2.142
[cns]
ocp-cns1.hpecloud.test
ocp-cns2.hpecloud.test
ocp-cns3.hpecloud.test
~~~

These playbooks use ansible-vault to encrypt sensitive login credentials. Modify passwords.yaml and vault it like so:

~~~
$ ansible-vault encrypt passwords.yaml --output=roles/passwords/tasks/main.yaml
~~~

The Satellite task assumes one NIC for provisioning and a bonded pair of 10gig NICs for all other traffic. Modify _interface_attributes_ in _roles/provisioning/tasks/satellite.yaml_ if the NIC configuration differs.

# run it

~~~
$ ansible-playbook -i hosts playbooks/provisioning.yaml --ask-vault-pass
~~~

There are additional playbooks to expore that provides some common configuration, as well as pre-deployment and post-deployment steps for deploying OpenShift with Container Native Storage.
