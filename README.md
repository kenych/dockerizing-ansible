Running ansible on docker.
You may ask why? Well, many reasons, first of all pure curiosity on how to do it, second, 
you may end up in environment where you don't have ansible installed nor you have a 
permissions to install anything, but free to pull docker images, a sort of immutable infrastructure.

You will also learn how to dockerize an existing tool, and plus have a chance to play with ansible and ansible-playbook.

```
docker build -f Dockerfile.ansible.cnf -t myansible .

docker build  -t my-docker-ssh .

docker run --rm -P -d  --name my-docker-ssh1  my-docker-ssh
docker run --rm -P -d  --name my-docker-ssh2  my-docker-ssh

ssh-keygen -f ansible-key

ssh-copy-id -f  -i ansible-key root@localhost -p $(docker port my-docker-ssh1  |grep 22 | sed 's/.*://g')


ssh -i ansible-key -p $(docker port my-docker-ssh  |grep 22 | sed 's/.*://g') 'root@localhost' ls -l /

docker run --name myansible --rm  -v `pwd`/ansible-key:/root/.ssh/id_rsa  myansible  ansible all -m raw -a "ls /" -i '172.17.0.2,' 

docker run --name myansible --rm  -v `pwd`/ansible-key:/root/.ssh/id_rsa  myansible  ansible \
 all -m raw -a "ls -l /"   -i \
 "$(docker inspect my-docker-ssh --format {{.NetworkSettings.Networks.bridge.IPAddress}})",


    
docker run --name myansible --rm -v `pwd`/ansible-key:/root/.ssh/id_rsa -v `pwd`/hosts:/etc/ansible/hosts  myansible  ansible docker-ssh -m raw -a "ls -l  /"


docker run --name myansible --rm -v `pwd`/ansible-key:/root/.ssh/id_rsa -v `pwd`/hosts:/etc/ansible/hosts  myansible  ansible docker-ssh -m raw -a "apt-get install nginx -y; service nginx restart"

docker run --name myansible --rm -v `pwd`/ansible-key:/root/.ssh/id_rsa -v `pwd`/hosts:/etc/ansible/hosts  -v `pwd`/test-playbook.yml:/tmp/test-playbook.yml  myansible  ansible-playbook -vvv /tmp/test-playbook.yml


```



https://ifritltd.com/2017/10/20/running-ansible-as-docker-container/

