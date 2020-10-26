FROM ubuntu:16.04

ARG ssh_prv_key
ARG ssh_pub_key

RUN apt-get update && \
    apt-get install -y git

# Authorize SSH Host
RUN mkdir -p /root/.ssh && \
    chmod 0700 /root/.ssh 

# Add the keys and set permissions
RUN echo "$ssh_prv_key" > /root/.ssh/id_rsa && \
    echo "$ssh_pub_key" > /root/.ssh/id_rsa.pub && \
    chmod 600 /root/.ssh/id_rsa && \
    chmod 600 /root/.ssh/id_rsa.pub
	
#Python 2.7 & Ansible installation
RUN apt install git python-pip -y &&\
    pip install ansible==2.9.12
