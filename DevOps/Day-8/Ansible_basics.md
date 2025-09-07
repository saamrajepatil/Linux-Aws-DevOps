Ansible and Ansible Architecture

What is Ansible?
Ansible is a simple, open-source, IT automation engine that automates cloud provisioning, intra-service orchestration, configuration management, application deployment, and many other IT needs in day to day life in Software engineer.

It is designed for multi-tier deployments. Thereby it aligning with the cloud’s basic structure precisely. Ansible models your IT infrastructure by describing how all of your systems inter-relate, rather than just managing one system at a time.

Automation simplifies complex tasks, not just making developers’ jobs more manageable but allowing them to focus attention on other tasks that add value to an organization. In other words, it frees up time and increases efficiency.

Why do we like Ansible?
Ansible helps in modeling the cloud IT infrastructure with a description of inter-relation between all systems. Ansible does not implement any agents or additional custom security infrastructure. Furthermore, Ansible also offers flexibility for deployment and uses the simple YAML language in its Playbooks. Therefore, the user could easily describe automation jobs in simple English.

Advantages of Ansible
Free: Ansible is an open-source tool.
Very simple to set up and use: No special coding skills are necessary to use Ansible.
Powerful: Ansible lets you model even highly complex IT workflows.
Flexible: You can orchestrate the entire application environment no matter where it’s deployed. You can also customize it based on your needs.
Agentless: You don’t need to install any other software or firewall ports on the client systems you want to automate. You also don’t have to set up a separate management structure.
Efficient: Because you don’t need to install any extra software, there’s more room for application resources on your server.
Ansible Architecture
The Ansible orchestration engine interacts with a user who is writing the Ansible playbook to execute the Ansible orchestration and interact along with the services of private or public cloud and configuration management database. The below diagram explains it.


![alt text](https://user-images.githubusercontent.com/51783400/195005036-dac589af-3c51-44bb-9ce9-4f8462d2e098.png)

![alt text](https://user-images.githubusercontent.com/51783400/195005121-be4e2f03-6bb8-4583-ac5e-7368b1b823e2.png)

Playbooks here actually define your workflow because whatever tasks that you write in a playbook, it gets executed in the same order that you have written them. They are written in YAML format, which describes the tasks and executes through the Ansible. Also, you can launch the tasks synchronously and asynchronously with playbooks.

The above architecture has a bunch of Host machines to which ansible server connects and pushes the playbooks through SSH. It is not always needed to use an SSH for connecting with your host machines; you can also use a connection plug-in. For example, Ansible provides you with a docker container connection plugin and using that connection plug-in, you can easily connect to all your Docker containers and start configuring right away.

It has an Ansible automation engine using which users can directly run a playbook that gets deployed on the hosts. There are multiple components in the Ansible automation engine. The first is a host inventory. It’s a list of all the IP addresses of all the hosts. Let’s go one by one, in the Ansible automation engine.

Ansible works against multiple systems in your infrastructure at the same time. It does this by selecting portions of systems listed in Ansible’s inventory file, which defaults to being saved in the location /etc/ansible/hosts. You can specify a different inventory file using the -i <path> option on the command line.
