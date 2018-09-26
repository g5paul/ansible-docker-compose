FROM amazonlinux:latest

RUN yum install -y openssh-server sudo && \
  /usr/bin/ssh-keygen -A && \
  systemctl enable sshd.service && \
  mkdir /var/run/sshd && \
  useradd ansible -s /bin/bash && \
  mkdir -p /home/ansible/.ssh/ && \
  chmod 0700 /home/ansible/.ssh/

RUN yum install -y procps && \
    yum install -y iproute

COPY ssh_config /home/ansible/.ssh/config
COPY ansible /home/ansible/.ssh/id_rsa
COPY ansible.pub /home/ansible/.ssh/id_rsa.pub
COPY ansible.pub /home/ansible/.ssh/authorized_keys

RUN chmod 0600 /home/ansible/.ssh/id_rsa
RUN chmod 0600 /home/ansible/.ssh/authorized_keys

RUN chown -R ansible:ansible /home/ansible/ && \
  echo "ansible ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers && \
  touch /etc/sysconfig/network

ENV container docker

EXPOSE 22
CMD ["/sbin/init"]
