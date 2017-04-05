## isam-ansible docker image

### Overview

This docker image is built on top of CentOS 7, it uses the Python package shipped with the OS and added Ansible, ISAM Python module, ISAM Ansible module, and ISAM Ansible Roles. 

The goal is to allow a user to run ISAM Ansible playbook quickly without installing a build system from scratch. 

### Pre-Requisites

You need to install Docker from www.docker.com on your system. The community edition is sufficient. 

### Build the image

You may pull the image from Docker Hub directly, or modify the Dockerfile and build your own image with the following command:

```
$ docker build -t mludocker/isam-ansible .
```

* You may also use your own tag for the image. *

### Run the container

Prepare your playbook and inventory files, then run the following command to start the playbook

```
$ docker run -it --rm -v <local directory to playbook>:/ansible/playbooks --name test mludocker/isam-ansible -i <inventory file> <playbook> -e "other environment variables" -vvv
```

The option '-v' maps a local directory to '/ansible/playbooks' inside the docker container. You may adjust it to fit your own environment. 

Here is an example:

```
$ docker run -it --rm -v $(pwd)/isam-ansible-playbook-sample:/ansible/playbooks  --name test mludocker/isam-ansible  -i inventories/demo demo.yml -vvv
```

This command maps the *isam-ansible-playbook-sample* subdirectory of the current location to /ansible/playbooks inside the docker container. 

### Use docker-compose to automate the execution

You may also create a docker-compose.yml file to specify the local volume mapping and other information, for example:

```
version: ‘2’
services:
 isam-ansible:
   image: “mludocker/isam-ansible”
   volumes:
   - $PWD:/ansible/playbooks
```

In the example above ‘isam-ansible’ is the name of the service you defined, you can change it to fit your needs. The volumes section maps your local directory to ‘/ansible/playbooks’ inside the docker container during runtime, you may change or add mappings to based on your own environment.

Save this file somewhere and refer it with ‘-f <path>’ option of the docker-compose command. If ‘-f <path>’ is not specified, docker-compose will search local directory and its parent directories.

```
docker-compose -f ~/docker-compose.yml run isam-ansible -i inventory site.yml
```

You may also create multiple docker-compose files and store all the command line options inside the file, and simply specify the docker-compose file.

For example, you can create a file docker-compose-production.yml like this:

```
version: ‘2’
services:
 isam-ansible:
   image: “mludocker/isam-ansible”
   volumes:
   - /home/build/playbooks/production:/ansible/playbooks
   command: -i inventories/production site.yml -vvv
```

Then run this command:

```
docker-compose -f docker-compose-production.yml up
```
