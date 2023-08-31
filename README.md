.
*** Link to application: https://brave-fog-06929.pktriot.net/
.
.
ANSIBLE PLAYBOOKS
1. To deploy single node Kubernetes cluster:
  - kubernetes_install.yml
2. To deploy the Todo app:
  - todo-app-install.yml



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
