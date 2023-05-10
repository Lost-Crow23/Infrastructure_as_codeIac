<h1>Ansible Controller</h1>

![Ansible Diagram v2](https://github.com/Lost-Crow23/Infrastructure_as_codeIac/assets/126012715/4e0cdcc6-be26-44d1-a74a-5d7997270a0b)

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
      # tech221-ansible-setup

      ```
      # -*- mode: ruby -*-
      # vi: set ft=ruby :
 
      # All Vagrant configuration is done below. The "2" in Vagrant.configure
      # configures the configuration version (we support older styles for
      # backwards compatibility). Please don't change it unless you know what
 
      # MULTI SERVER/VMs environment 
      Vagrant.configure("2") do |config|
      # creating are Ansible controller
      config.vm.define "controller" do |controller|
     
      controller.vm.box = "bento/ubuntu-18.04"
    
      controller.vm.hostname = 'controller'
    
      controller.vm.network :private_network, ip: "192.168.33.12"
    
      # config.hostsupdater.aliases = ["development.controller"] 
    
    end 
    
      # creating first VM called web  
      config.vm.define "web" do |web|
     
      web.vm.box = "bento/ubuntu-18.04"
      # downloading ubuntu 18.04 image
 
      web.vm.hostname = 'web'
      # assigning host name to the VM
     
      web.vm.network :private_network, ip: "192.168.33.10"
      # assigning private IP
     
      #config.hostsupdater.aliases = ["development.web"]
      # creating a link called development.web so we can access web page with this link instread of an IP   
         
    end
   
      # creating second VM called db
      config.vm.define "db" do |db|
     
      db.vm.box = "bento/ubuntu-18.04"
     
      db.vm.hostname = 'db'
     
      db.vm.network :private_network, ip: "192.168.33.11"
     
      #config.hostsupdater.aliases = ["development.db"] 
     
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
    
<h2>Ansible Guide</h2>

Errors encountered: Had to change my web IP address in the `vagrantfile` on VS code to `192.168.56.105` since the previous IP address was giving errors when following the steps as below from `192.162.56.100`.
This is step by step by guide if running into errors in a Mac high seirra. 

<h3>Step 1</h3>

- Make sure you can `ssh` onto the other agents using the controller terminal. e.g. `ssh vagrant@192.168.56.105`
- Prompts a password: `vagrant` or whatever password is setup 
- Do the same for the `db` agent in contoller using the db IP address to check if it is working `ssh vagrant@192.168.56.110`

<h3>Step 2</h3>

- `sudo apt install tree` to make the `ls` command look more cleaner

<h3>Step 3</h3>

- `cd` from controller to `/etc/ansible/`

In the folder `/etc/ansible/` there is a hosts file. In here is where you can specify the ip addresses of things you want look into.

- `sudo nano hosts` , scroll down and enter with your IP addresses and the command lines below.


      [web]
      192.168.56.105 ansible_connection=ssh ansible_ssh_user=vagrant ansible_ssh_pass=vagrant

      [db]
      192.168.56.110 ansible_connection=ssh ansible_ssh_user=vagrant ansible_ssh_pass=vagrant
      
<h3>Step 4</h3>

- `sudo nano ansible.cfg`

- Make sure to edit this file and add or uncomment the `default` section to `host_key_checking = False` 

<h3>Step 5</h3>

- `sudo ansible web -a "uname"` but we used `sudo ansible db -a "uname"` to display OS system we used as we wanted to configure and see this command when executing this agent. 
- `sudo ansible web -a "uname"` so `sudo ansible web -a "date"` to display the date we used to configure and see this command when executing this agent.
- Alternatvely, you may use `ansible all -a` is a command to run a command across all hosts.
- And `sudo ansible all -a "ls" --ask-vault-pass` which will prompt you to enter the password `vagrant` and it will display all the working agents. 

      Deprecation warnings can be disabled by setting deprecation_warnings=False in ansible.cfg.
      192.168.56.105 | CHANGED | rc=0 >>

      Deprecation warnings can be disabled by setting deprecation_warnings=False in ansible.cfg.
      192.168.56.110 | CHANGED | rc=0 >>

<h3>Step 6</h3>

We may use this command to show if everything is successful within the agents.

`sudo ansible name(web) -m ping`

      192.168.56.105 | SUCCESS => {
            "ansible_facts": {
                   "discovered_interpreter_python": "/usr/bin/python"
            }, 
            "changed": false, 
            "ping": "pong"
      }


Extras:

To reboot all machines you can just use the shell extension in ansible:

      sudo ansible all -a "/sbin/reboot"
      
Similarly for uptime:

      sudo ansible all -m shell -a "uptime"
      
<h2>Using ADHOC method</h2>

An Ansible ad hoc command uses the `/usr/bin/ansible` command-line tool to automate a single task on one or more managed nodes.

Using this command we can successfully copy a file from the `/etc/ansible` directory on the control machine to the `/home/vagrant` which is our `web` host.

      `sudo ansible web -m copy -a "src=/etc/ansible/testing.txt dest=/home/vagrant"` 

      sudo ansible: This is the command to run an Ansible playbook or module.
      web: Name of the Ansible inventory group that represents the "web" host.
      -m copy: This specifies that we want to use the "copy" module to copy a file.
      -a: This option specifies the arguments to pass to the module. In this case, we're using the following arguments:
      `src=/etc/ansible/testing.txt:` This specifies the source file we want to copy, which is located in the "/etc/ansible" directory on the control machine.
      `dest=/home/vagrant`: This specifies the destination directory where we want to copy the file on the "web" host.

When you run this command, Ansible will connect to the "web" host, copy the file from the "/etc/ansible" directory on the control machine to the "/home/vagrant" directory on the "web" host, and report back the result.
    
<h2>Ansible Playbook</h2>

An Ansible playbook is a collection of tasks that are executed on one or more hosts defined in an inventory. Playbooks are written in YAML format and are used to automate tasks such as software installations, configuration management, and application deployments. Playbooks can also include variables, conditionals, loops, and modules to perform complex tasks.

Installing Nginx

- Written in yaml
- Instructions to talk to servers
- Multiple playbooks for multiple servers

      # This is a playbook to install and set up Nginx in our web server (192.168.33.10)
      # This playbook is written in YAML and YAML starts with three dashes (front matter)
      # YAML file starts ---
      ---
      # where would you like to install nginx
      # name of the hosts - hosts is to define the name of your host of all
      - hosts: web

      # Would you like to see logs
      # find the facts about the host
      gather_facts: yes

      # Do we need admin access - sudo
      # admin access
      become: true

      # Add the instructions - commands
      tasks:
      - name: Install Nginx in web server
        #install nginx
        apt: pkg=nginx state=present update_cache=yes
      # Ensure status is running/active
      # restart nginx if reverse proxy is implemented or if needed
       notify:
             - restart nginx
      - name: Allow all access to tcp port 80
      ufw:
            rule: allow
            port: '80'
             proto: tcp

      handlers:
       - name: Restart Nginx
            service:
            name: nginx

- Run the command `sudo ansible-playbook install-nginx-playbook.yml` to execute the playbook
- To check the status of the running file `sudo ansible web -a "systemctl status nginx"`

<h2>Ansible Playbook Version 2 - Installing Nodejs</h2>

<h3>Step 1</h3>

- Make a new playbook script - `sudo nano install-nodejs-playbook.yml`
- Enter the following commands as below: you may have errors with the indentation so please do fix it or else it will through errors when ran. 
- `cat install-nodejs-playbook.yml`

---
      - hosts: web
        become: true
        tasks:
        - name: Install nodejs in web server
          apt: 
            name: nodejs 
            state: present
        - name: Install npm in web server
          apt: 
            name: npm 
            state: present
        - name: Install python in web server
          apt: 
            name: python 
            state: present

<h3>Step 2</h3>

`sudo ansible web -a "nodejs --version"`

- To check the version of node, should be given as `v8.10.0` if working.

<h3>Step 3</h3>

- `sudo ansible-playbook install-nodejs-playbook.yml` to run the playbook
- Now `nginx` should at least work.

<h3>Step 4</h3>

Now we are going to launch the app with the web IP with port 3000. 

`scp -r /path/to/source/folder user@remote:/path/to/destination/folder` 

Command to copy one file to another destination.

-  `scp -r /Users/Admin/Documents/Virtualisation/app vagrant@192.168.56.105:/home/vagrant`
- This command is run in your git bash terminal in the `vagrantfile` where you would `cd` into the current file then copy the `app` folder into the folder to where you are executing the current folder within. Make sure to use your web IP correctly. 
- This command should also prompt a password which is the same password as you ssh into the web agent, `vagrant`

<h3>Step 5</h3>

- Go back to the `vagrant@controller` git bash terminal and `cd` onto the web agent.
- Enter the password, `vagrant`
- Make sure to `cd app` as we should now have the app folder within the agent as we inputted the IP address as before. 

<h3>Final Iteration</h3>

`node app.js -y`

- This command will run the node app and should be now listening on port 3000.

<img width="1402" alt="Sparta app, ansible " src="https://github.com/Lost-Crow23/Infrastructure_as_codeIac/assets/126012715/85625bb9-649a-49a6-aafc-0012bbb0fe6a">

<h2>Ansible playbook version 3 - Connecting our Mongodb Database</h2>

<h3>Pre-requisites</h3>

- To have `web`, `db` and `controller` running in the VM `virtualbox` if `saved`, `vagrant up` to initialise the VMs again.

<img width="736" alt="running all vms on virtualbox" src="https://github.com/Lost-Crow23/Infrastructure_as_codeIac/assets/126012715/e463905f-f524-4acb-af4e-d2e7c9a3595b">

<h3>Step 1</h3>

On Git bash terminal `ssh` into the `vagrant@controller`:

- Since we `saved` our VMs we can use the command `sudo ansible all -m ping` to display the working agent nodes if they are successful or not.

<h3>Step 2</h3>

- `cd` into the `etc/asnible/` and create a new playbook

<h3>Step 3</h3>

- You may name the new playbook as you wish, but for staying in context we did `sudo nano mongo-db-playbook.yml`
- Edit the nano file to configure mongodb

<h3>Step 4</h3>

Enter the following commands onto the nano file: making sure our host is to our `db` agent not `web`

      ---
      # setting up mongodb in DB-agent-node
      # where would you like to install mongo db
      - hosts: db
      # Would you like to see logs
        gather_facts: yes
      # Do we need admin access - sudo
        become: true
      # Add the instructions - commands for mongodb and status running
        tasks:
        - name: configuring Mongo-db in db agent node
          apt: pkg=mongodb state=present
      # check status with Adhoc commands
     
<h3>Step 5</h3>
 
- Run the playbook using: `sudo ansible db -a "systemctl status mongodb"`

- You should have this shown:

<img width="553" alt="Running mongodb Iac" src="https://github.com/Lost-Crow23/Infrastructure_as_codeIac/assets/126012715/855e421f-f103-4e15-85c9-3a7502bce347">

<h3>Step 6</h3>

- From the `db` agent node, we need to first `ssh vagrant@192.168.56.110` which is our db IP to get into the agent node.
- `cd` into the `/etc/` folder to which were our mongod.conf file is in.

<h3>Step 7</h3>

- To configure use `sudo nano mongodb.conf` which should open up the mongod file
- All we do is change the `bindip` to `0.0.0.0` 

Step 8

- `sudo systemctl restart mongodb`, to restart mongodb
- `sudo systemctl enable mongodb`, to enable mongodb
- `sudo systemctl status mongodb`, to check the statuss of mongodb

Step 9 

- From our now `web` agent node, we need to export the DB_HOST onto our environment variable and to do this, we use:

`export DB_HOST=mongodb://192.168.56.110:27017/posts`

- With our `db` IP address and the mongodb port number to display our sparta app configured within the app folder.

- `printenv DB_HOST` to display the environment variable within our `web` agent node.

Step 10 

- `cd app` and `ls` and run the `npm install` and the `npm start`
- This should now work and the sparta app should be displayed through the `web app` agent node IP with `3000` and `3000/posts`

Diagram 

Automating the bindIp on DB agent node

We created two playbooks to execute this.

Step 1 

- Create a new playbook called `sudo nano mongo.conf.yml` to automate the bindIP on the file of where it is within the `db` node agent.
- Once created we enter the commands as below to change the Ip:
```
            ---
            - hosts: db

              gather_facts: yes

              become: true

            #configure mongodb.conf
              tasks:
            - name: change bing_ip in mongodb.conf
             #adds or modifies a line in the /etc/mongod.conf
              lineinfile:
                   path: /etc/mongodb.conf
             #parameter matches any existing bindIp lines, and the line parameter adds a new bindIp 
                   regexp: '0.0.0.10'
                   line: '0.0.0.0'
             #This creates a backup of the file before modifying it      
                   backrefs: yes

            - name: restart mongodb
              shell: systemctl restart mongodb

            - name: enable mongodb
              shell: systemctl enable mongodb

```


