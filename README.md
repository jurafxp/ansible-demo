Building and deploying demo Application using Ansible Playbooks.
-------------------------------------------

This playbook is expecting CentOS 7.x host and targets. On Ansible host is
build java application using Maven and then deployed on target webservers

Deployment was tested on AWS image "CentOS 7 (x86_64) - with Updates HVM"
on Free Tier EC2 instances.

Application can be on a single node or multiple nodes. The inventory file
'hosts' defines the nodes in which the stacks should be configured. 

```
[webserver]
webserver1 ansible_user=user ansible_become=true
```

To start the deployment on ansible host, use the following steps:

1. yum install ansible git
2. git clone https://github.com/jurafxp/ansible-demo.git
3. ANSIBLE_HOST_KEY_CHECKING=False ansible-playbook -i hosts ansible-demo/site.yml

The ssh private key for the target webservers is expected on Ansible host.
Either in hosts file with "ansible_ssh_private_key_file" parameter or add
the ssh key into SSH agent.

Nginx is using self-signed certificates.
PostgreSQL is used as database backend for the demo application.

Application is cloned ditectly from original repo and then patched to use
PostgreSQL database backend. On production, forking the repo and make
modifications on the fork would be probably cleaner solution.

There are no tests if the application is running correctly, only that the
playbook deployment tasks ends correctly.

After deployment, you can use REST API of the demo application described on
https://github.com/khoubyari/spring-boot-rest-example.

Simple test of the application can be done using "curl":

```
curl -k 'https://webserver1/example/v1/hotels?page=0&size=10'
{"content":[],"last":true,"totalPages":0,"totalElements":0,"size":10,"number":0,"first":true,"sort":null,"numberOfElements":0}
```

Demo application can be controlled also directly using "supervisord":

```
Start application:
supervisorctl stop spring-boot-rest-example
spring-boot-rest-example: stopped
Stop application:
supervisorctl start spring-boot-rest-example
spring-boot-rest-example: started
```