<h1>Ansible Controller</h1>

Diagram

<h2>Pre-requisites</h2>

- Ansible, Configuration Management 
- Vagrant: For dev-env tools
- AWS: Cloud formation AWS
- Ansible is useful as it automates configuration of VMs such as IP address changes, it'll automatically go around and replace IP addresses in 
configuration files should they update
- Jenkins CI/CD

<h2>Ansible controller and agent nodes set up</h2>

- Create a new folder within the VSCode called "Iac" then make a new file called `vagrantfile` clone or copy and paste the `vagrantfile` codes and save.
- Clone the `vagrantfile` from "" and then `vagrant up`.
- Make sure to `double check syntax/indentation)`

<h2>Using Ubuntu 18.04 for ansible controller for the agent nodes set up </h2>

<h3>Step 1</h3>

- Refer back to the vagrant documentation `link` and follow the neccessary steps if need be

<h3>Step 2</h3>

- Install all necessary plugins or dependencies required depending on the OS you are using. 

      # -*- mode: ruby -*-
      # vi: set ft=ruby :

      # All Vagrant configuration is done below. The "2" in Vagrant.configure
      # configures the configuration version (we support older styles for
      # backwards compatibility). Please don't change it unless you know what

      # MULTI SERVER/VMs environment 

      Vagrant.configure("2") do |config|

      # creating first VM called web  
      config.vm.define "web" do |web|
    
      web.vm.box = "bento/ubuntu-18.04"
      # downloading ubuntu 18.04 image

      web.vm.hostname = 'web'
      # assigning host name to the VM
    
      web.vm.network :private_network, ip: "192.168.56.150"
      #   assigning private IP
    
      config.hostsupdater.aliases = ["development.web"]
      # creating a link called development.web so we can access web page with this link instread of an IP   
        
    end
  
      # creating second VM called db
      config.vm.define "db" do |db|
    
      db.vm.box = "bento/ubuntu-18.04"
    
      db.vm.hostname = 'db'
    
      db.vm.network :private_network, ip: "192.168.56.100"
    
      config.hostsupdater.aliases = ["development.db"]     
    end

    #creating are Ansible controller
    
    config.vm.define "controller" do |controller|
    
    controller.vm.box = "bento/ubuntu-18.04"
    
    controller.vm.hostname = 'controller'
    
    controller.vm.network :private_network, ip: "192.168.56.110"
    
    config.hostsupdater.aliases = ["development.controller"] 
    
    end

    end


<h2>Controller setup </h2>

After running vagrant up, SSH in to the controller and run the following commands:

    sudo apt-get update
    sudo apt upgrade -y
    sudo apt-get install software-properties-common
    sudo apt-add-repository ppa:ansible/ansible
    sudo apt-get update
    sudo apt-get install ansible
    ssh vagrant@webIP # password is vagrant
