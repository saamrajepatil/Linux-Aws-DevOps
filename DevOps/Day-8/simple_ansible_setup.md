Create 2 EC2 with Amazon linux 2023
1) Master node
2) Worker Node

login to your master node using ec2-user

[root@ip-172-31-95-153 ~]# sudo su ec2-user

Install ansible using ec2-user

[ec2-user@ip-172-31-95-153 ~]$ sudo dnf install ansible

Password-less auth setup:

Genrate keys using ssh-keygen command

[ec2-user@ip-172-31-95-153 ~]$ ssh-keygen

press enter enter till keys got genrated.

Then copy the below pub key and keep in notepad.

cat /home/ec2-user/.ssh/id_rsa.pub

Login to worker node :
1) sudo su ec2-user
2) mkdir -p ~/.ssh
3) touch ~/.ssh/authorized_keys
4) vi ~/.ssh/authorized_keys
5) enter the key cpoied on notepad to new line of of this file
6) chmod 700 ~/.ssh
7) chmod 600 ~/.ssh/authorized_keys

now your setup is done 
test the setup from master node you are able to do ssh into worker node without password
like 
ssh ec2-user@172.31.83.18
ssh ec2-user@<private-ip-ofworker-node>
you will be able to login the worker node without password.
then enter exit.

vi /etc/ansible/hosts
paste private ip of worker node

1)ansible all -m ping

2) ansible all -a "df -h"
   
3) ansible all -a "uptime"

