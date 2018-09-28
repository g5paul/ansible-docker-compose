# ansible-docker-compose

*This repo forked from [ansible docker compose](https://github.com/andymotta/ansible-docker-compose). Updated to work with Container OS Amazon Linux 2 and Centos 7 on Host OS Ubuntu 16.04.5 LTS.*

- *Docker Compose support tested on Host OS Ubuntu 16.04.5 LTS with Container OS Centos 7*
   - *Docker version 18.06.0-ce*
   - *docker-compose version 1.8.0*
- *Updated support for Amazon Linux 2 for Container OS*
- *Updated docker compose file to version 2*
- *Original Mac OS X support has not been re-tested*

The purpose of this project is to be able to test Ansible roles locally and in Docker containers, with docker-compose.

To provide an example, included are roles to create a complete local stack load-balanced by HAProxy and served by Nginx, [Compliments of Sysadmin Casts. ](https://sysadmincasts.com/episodes/47-zero-downtime-deployments-with-ansible-part-4-4)

The control node is Ubuntu/trusty and target "hosts" are either Amazon Linux 2 with init and SSH access or Centos 7 with systemd and SSH access.  Basically, not the way you want to run containers but both serve their purpose as lightweight training environments.

### Getting Started
First install Docker and Docker Compose:
 - Docker: Linux: https://docs.docker.com/install/
 - Docker Compose: Linux: https://docs.docker.com/compose/install/

To build and run Amazon Linux containers on Ubuntu host run the following:
```bash
chmod 0600 ./env/ansible*
docker-compose -f docker-compose.yml -f docker-compose-host-ubuntu.yml build
docker-compose -f docker-compose.yml -f docker-compose-host-ubuntu.yml up -d
cat env/ssh_host_config >> ~/.ssh/config
ssh control # do this from the repo root
```

To build and run Centos containers on Ubuntu host run the following:
```bash
chmod 0600 ./env/ansible*
docker-compose -f docker-compose.yml -f docker-compose-container-centos.yml -f docker-compose-host-ubuntu.yml build
docker-compose -f docker-compose.yml -f docker-compose-container-centos.yml -f docker-compose-host-ubuntu.yml up -d
cat env/ssh_host_config >> ~/.ssh/config
ssh control # do this from the repo root
```

### Running the example playbooks from 'control' node:
```bash
ansible@control:~$ cd ansible/
ansible@control:~/ansible$ ansible-playbook site.yml
ansible@control:~/ansible$ ansible-playbook playbooks/stack_status.yml`
open http://localhost:8001 # on your local Host 
```

After HAProxy role finishes, or when applying *ansible-playbook blog.yml*, continuously refresh <http://localhost:8001/haproxy?stats> to watch the backends being taken out of service 50% at a time to apply the blog.

`curl http://localhost:8001` a few times.  You will see "Served by web1/web2" cycling.

To see Apache benchmarks run `ansible-playbook playbooks/stack_status.yml`

### Vault
If you want to add any secrets, store them in ansible-vault:
`ansible-vault edit group_vars/all/vault`
* **Note**: This works because the vault password is injected during the Dockerfile-control build.  For testing only.

After you edit the vault, add the variable to `group_vars/all/vars.yml`
An example is included.

### How to re-build Docker images for Amazon Linux containers on Ubuntu host:
```bash
docker-compose -f docker-compose.yml -f docker-compose-host-ubuntu.yml down
docker-compose -f docker-compose.yml -f docker-compose-host-ubuntu.yml build
docker-compose -f docker-compose.yml -f docker-compose-host-ubuntu.yml up -d
```

### How to re-build Docker images for Centos 7 containers on Ubuntu host:
```bash
docker-compose -f docker-compose.yml -f docker-compose-container-centos.yml -f docker-compose-host-ubuntu.yml down
docker-compose -f docker-compose.yml -f docker-compose-container-centos.yml -f docker-compose-host-ubuntu.yml build
docker-compose -f docker-compose.yml -f docker-compose-container-centos.yml -f docker-compose-host-ubuntu.yml up -d
```

#### Final Note:
Be sure not to copy the private key *env/ansible* anywhere.  It was generated for the purposes of this test stack, not to be uploaded anywhere else.


