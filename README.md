
*** Link to application: https://brave-fog-06929.pktriot.net/

ToDo app application provided by Docker, deployed in a single-node Kubernetes cluster and integrated with MySQL DB with persistent storage.
_____________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________

REPOSITORY FILES SUMMARY:
1. hosts
  - Ansible hosts file where the destination VM address, that will host the ToDo app, is configured
  - the file needs to be created in the same directory where the ansible playbooks are located
2. kubernetes_install
  - Ansible playbook used to deploy the Kubernetes cluster
3. todo-app-install
  - Ansible playbook used to deploy the ToDo app 
_____________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________

SOLUTION DESCRIPTION:

To deploy this aplication I used two Virtual Machines with Ubuntu 20.04 OS, placed in the same subnet. On VM1 I installed Ansible and I configured the host file to include the IP address of VM2. After that, I generated an ssh key and copied it to VM2, to allow login via ssh without a password.

When Ansible was installed and VM1 configuration comlpeted, I deployed the Kubernetes cluster on VM2 using the ansible playbook kubernetes_install.yml.
- Kubernetes installation
   - Kubernetes consists of a single-node k8s cluster
   - a new user (k8sadmin) was created to interact with k8s cluster
   - CRI-O was implemented as container runtime engine
   - Calico was implemented as Network plugin
   - The master node was taint, in order to allow pods to run on it, without additional worker nodes

All the and tools needed to deploy the ToDo app are deployed via the ansible playbook todo-app-install. Since the application is provided by Docker, I installed Docker also on VM2, to be able to create the image of the app.
- Docker installation
    - the GIT repository that contains the app files was cloned locally
    - the Dockerfile was created, with instructions to create the image
    - "todo-app-img" wsa built from the Dockerfile
    - "todo-app-img" was then pushed to the Docker hub, in a public repository

In the next steps, the MySQL DB was deployed and configured.
- MySQL installation
    - "mysql" Namespace was created, ehere both MySQL DB and ToDo app will be deployed
    - "efs-sc" Storage Class that will accomodate the Persistance Volume was created
    - "mysql-pv" Persistance Volume was created
    - "mysql-pvc" Persistance Volume Claim was created, to be used by the MySQL DB
    - "mysql" Pod was created and configured to use "mysql-pvc" as volume
    - "mysql" Service was created, with port 3306 exposed

When MySQL DB configuration was completed, the ToDo app was deployed:
- ToDo app installation
    - "todo-app" Pod was created and configured via environment variables to use the MySQL as it's DB
    - "todo-app" Service was created, with target port set to 3000

After the Deployment of ToDo app, the Ingress service was configured:
- Ingress installation
    - NGINX was installed as Ingress Controller
    - "todo-ingress" Service was created, configured to use NGINX as Ingress Controller, and configured to present the "todo-app" Service over HTTP, on host todoapp.example.com, at the default "/" path
_____________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________

PREREQUISITES:
- VM1:
   - install Ansible:
      - sudo apt install ansible
   - edit /etc/hosts and add the IP address and hostname of VM2
   - reboot VM1
       - sudo reboot
   - Generate SSH key and copy it on VM2, as root:
       - su root
       - ssh-keygen
       - cd .ssh/
       - ssh-copy-id root@<VM2_hostname>
_____________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________


DEPLOYMENT STEPS:
- 


    l. Ansible install
	- Add hostname in /etc/hosts
	- sudo apt install ansible
	- Create ssh keyes:
		- ssh-keygen
		- cd .ssh/
		- ssh-copy-id root@kubernetes
		
	2. Kubernetes Install: https://www.checkmateq.com/blog/ansible-kubernetes
	- Install Git
		- sudo apt install git
	- Clone repository from git
		- git clone https://github.com/networknuts/ansible-k8s-setup.git 
	- Create a directory to store the config files
		- mkdir kube_files
	- Create the config files and run the ansible playbooks (Location PC: C:\Denis\kubernetes\Ansible deployment of kubernentes)
		- Setup inventory file
			- touch hosts
		- Set up a user on nodes (user: k8sadmin)
			- vi set_up_users.yml
				□ ansible-playbook -i hosts set_up_users.yml
		- Install CRI-O runtime and other Kubernetes dependencies on all nodes
			- vi install_cri-o.yml
				□ ansible-playbook -i hosts install_cri-o.yml
		- Initialize kubeadm on the master node
			- vi initialize_kubeadm_master.yml
				□ ansible-playbook -I hosts initialize_kubeadm_master.yml
	
	
	- On the Kubernetes VM, check the kubernetes cluster:
		- kubectl get nodes -o wide
		- Deploy a test nginx pod
			- kubectl create -f https://k8s.io/examples/application/deployment.yaml

	3. Todo app install
	- Install Docker
		- ansible-playbook -I hosts install_docker.yml
	- Install Todoapp
    - ansible-playbook -I hosts todo.yml
