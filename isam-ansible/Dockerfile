# isam-ansible docker version 2.0
FROM centos:7

ENV container docker

# Install OS pre-requisites
RUN yum -y update; yum clean all
RUN yum -y install epel-release; yum clean all
RUN yum -y install unzip gcc curl openssl-devel openssh-client git python python-pip python-crypto python-devel; yum clean all

# Install/Update Python modules
RUN pip install --upgrade pip requests ansible
RUN pip install --upgrade git+https://github.com/ibm-security/ibmsecurity#egg=ibmsecurity

# Remove packages to save space
RUN yum -y remove epel-release gcc openssl-devel

# Prepare Ansible environment
RUN mkdir /etc/ansible/ /ansible /etc/ansible/roles/
RUN echo "[local]" >> /etc/ansible/hosts && \
    echo "localhost" >> /etc/ansible/hosts

RUN mkdir -p /ansible/playbooks
WORKDIR /ansible/playbooks

# Prepare ISAM Ansible Common Roles 
RUN ansible-galaxy install -c git+https://github.com/ibm-security/isam-ansible-roles.git 

ENV ANSIBLE_GATHERING smart
ENV ANSIBLE_HOST_KEY_CHECKING false
ENV ANSIBLE_RETRY_FILES_ENABLED false
ENV ANSIBLE_ROLES_PATH /etc/ansible/roles/isam-ansible-roles
ENV ANSIBLE_SSH_PIPELINING True
ENV PATH /ansible/bin:$PATH
ENV PYTHONPATH /ansible/lib

ENTRYPOINT ["ansible-playbook"]

# Sample command to build image with this Dockerfile:
#docker build --rm -t mludocker/isam-ansible .

# Check out playbooks and inventories, then run the following command to start the playbook
# docker run -it --rm -v <local directory to playbook>:/ansible/playbooks --name test mludocker/isam-ansible -i <inventory file> <playbook> -e "other environment variables" -vvv
# for example:
# docker run -it --rm -v $(pwd)/isam-ansible-playbook-sample:/ansible/playbooks  --name test mludocker/isam-ansible  -i inventories/demo demo.yml -vvv
