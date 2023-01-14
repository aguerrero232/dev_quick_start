# **Development Quickstart** `Guide` 🖥️

This is intended to be an easy to follow guide for setting up a development environment on a fresh install of Ubuntu-22 Server. 


Items tagged with `(local)` are only needed on your local machine, and items tagged with `(server)` are only needed on your server.



## **System** ⚙️

* **`virtualization`**: ***VirtualBox*** 
  * https://www.virtualbox.org/wiki/Downloads
* **`operating system`**: ***Ubuntu-22 Server***
  * https://ubuntu.com/download/server
* **`ram`**: **8GB**
* **`cpu cores`**: **6** 
* **`storage`**: **60GB**

<br>

## **Windows** 🖥️

* ### **tabby.sh** ▶️ `(local)`

	**<a href="https://tabby.sh/">tabby.sh</a>**, is a **clean** cross-platform terminal app for local shells. Its easy to use, and has a lot of features. You'll use it to ssh into your servers, and run commands on them. With **tabbys** built in **`ssh`** and **`sftp`** support, you can easily manage your server(s). 


	* **download** it from their <a href="https://tabby.sh/">website</a> 

	<br>


* **ssh key** `(local)`

	for this part I used `powershell` (on tabby... they make it so easy... just saying...) on my windows machine, but you can use whatever you want

  * create a new key

  	```shell
  	ssh-keygen -t ed25519
  	```
  	* its going to ask you a lot of questions, but if you don't care about security, just press enter for all of them.

  * you should now have a new key in `~/.ssh/`
  	* copy the key located at **~/.ssh/id_ed25519.pub** 

  1. paste the key into your **githubs** ssh key settings
  2. paste the key in **~/.ssh/authorized_keys** on your `server`


* create a **ssh** config file `(local)`, to **make it easier** to ssh into your server from your local machine

	```shell
	New-Item -Path "C:\Users\<local-user-dir>\.ssh\config"
	```

	* add the following to the config file

		```bash
		# ~/.ssh/config
		Host <vm-host>
		HostName <host-ip-address>
		User <vm-user-name>
		# port22=ssh
		port 22 
		# location of private key
		IdentityFile C:\Users\<local-user-dir>\.ssh\<private-key-file>
		# DynamicForward 127.0.0.1:9999 
		LocalForward localhost:9998 localhost:9998
		LocalForward localhost:8004 localhost:8004
		LocalForward localhost:8003 localhost:8003
		LocalForward localhost:8002 localhost:8002
		LocalForward localhost:8001 localhost:8001
		LocalForward localhost:8080 localhost:8080
		LocalForward localhost:5432 localhost:5432
		LocalForward localhost:3000 localhost:3000
		LocalForward localhost:27017 localhost:27017	
		```

	* heres mine for example

		```bash
		# ~/.ssh/config
		Host parse-dev
		HostName 192.168.56.122
		User vrixxx
		# port22=ssh
		port 22 
		# location of private key
		IdentityFile C:\Users\holai\.ssh\id_ed25519
		# DynamicForward 127.0.0.1:9999 
		LocalForward localhost:9998 localhost:9998
		LocalForward localhost:8004 localhost:8004
		LocalForward localhost:8003 localhost:8003
		LocalForward localhost:8002 localhost:8002
		LocalForward localhost:8001 localhost:8001
		LocalForward localhost:8080 localhost:8080
		LocalForward localhost:5432 localhost:5432
		LocalForward localhost:3000 localhost:3000
		LocalForward localhost:27017 localhost:27017	
		```


## **Ubuntu** 🐧

* create a .nanorc file

	```shell
	touch ~/.nanorc
	```
	* run 

		```shell
		nano ~/.nanorc
		```

    * add the following to the .nanorc file

		```yaml
		# .nanorc
		set tabstospaces
		set tabsize 2
		```


### **~/.bashrc**

* sample of the final `~/.bashrc`, read the comments to understand what each line does and paste it at the bottom of your `~/.bashrc`

	```bash
	# ~/.bashrc
	...
	...
	...
	...
	# set default editor to nano
	export EDITOR=nano
	# set default editor for kubectl to nano (kubernetes)
	export KUBE_EDITOR="/bin/nano"
	# alias for kubectl, so you can just type kc instead of kubectl
	alias kc=kubectl

	# uncomment the following line if are using self-signed certs and get a warning about it
	# export PYTHONWARNINGS="ignore:Unverified HTTPS request"

	# scripts placed in here will be added to the path automatically, and can be run from anywhere in the terminal
	export SCRIPTS_ROOT="$HOME/scripts"
	export PYENV_ROOT="$HOME/.pyenv"

	# pyenv and scripts were added to the path
	#! remember to update the path as you add more things to it
	export PATH="$PYENV_ROOT/bin:$SCRIPTS_ROOT:$PATH"

	# init pyenv, virtualenv, and starship
	eval "$(pyenv init -)"
	eval "$(pyenv virtualenv-init -)"
	eval "$(starship init bash)"
	```

* install `net-tools` for `ifconfig` command

	```shell
	sudo apt install net-tools
	```

* update netplan for a `static ip`

	```shell
	sudo nano /etc/netplan/<name-of-config-file>.yaml
	```
	* add to the config file
		```yaml
		# /etc/netplan/<name-of-config-file>.yaml
		network:
		  ethernets:
			<interface-1>:
			  dhcp4: true
			<interface-2>:
			  addresses: [<static-ip>/24]
		  version: 2
		```

	* heres mine for example
		```yaml
		# /etc/netplan/00-installer-config.yaml
		network:
		  ethernets:
			enp0s3:
			  dhcp4: true
			enp0s8:
			  addresses: 
			    - 192.168.56.122/24
		  version: 2
		```

	* apply the changes

		```shell
		sudo netplan apply
		```

**ssh key**

* create a new key
	```shell
	ssh-keygen -t ed25519
	```
	* its going to ask you a lot of questions, but if you don't care about security, just press enter for all of them.

* you should now have a new key in `~/.ssh/`
	* copy the key to your clipboard
		```shell
		clip < ~/.ssh/id_ed25519.pub
		```
* paste the key into your **githubs** ssh key settings



## ***Starship*** `(server)` 🚀 


***Makes the terminal look cool***

* install starship
	```shell
	curl -fsSL https://starship.rs/install.sh | sh
	```
  * gets the latest version of <a href="https://starship.rs/">starship</a> and installs it 

* create and add the following to the `~/.config/starship.toml` 

	```shell
	mkdir -p ~/.config && touch ~/.config/starship.toml
	```

	```toml
	# ~/.config/starship.toml
	# Get editor completions based on the config schema
	"$schema" = 'https://starship.rs/config-schema.json'
	continuation_prompt = "▶▶"
	# Inserts a blank line between shell prompts
	add_newline = true
	format = "$all$directory$character"
	# Replace the "❯" symbol in the prompt with "➜"
	[character] # The name of the module we are configuring is "character"
	success_symbol = "[➜](bold green)" # The "success_symbol" segment is being set to "➜" with the color "bold green"
	# Disable the package module, hiding it from the prompt completely
	[package]
	disabled = true
	[docker_context]
	format = "via [🐋 $context](blue bold)"
	[env_var]
	variable = "SHELL"
	default = "unknown shell"
	[git_status]
	conflicted = "🏳"
	ahead = "🏎💨"
	behind = "😰"
	diverged = "😵"
	up_to_date = "✓"
	untracked = "🤷"
	stashed = "📦"
	modified = "📝"
	staged = '[++\($count\)](green)'
	renamed = "👅"
	deleted = "🗑"
	[kubernetes]
	disabled = false
	[python]
	pyenv_version_name = true
	version_format = "v${raw}"
	[username]
	style_user = "white bold"
	format = "[$user]($style) "
	disabled = false
	show_always = true
	[shell]
	disabled = false
	bash_indicator = " 🚀 "
	[gcloud]
	disabled = true
	```


## **Python** `(server)` 🐍

* **install** build dependencies

	```shell
	sudo apt install -y make build-essential libssl-dev zlib1g-dev \
	libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev \
	libncursesw5-dev xz-utils tk-dev libffi-dev liblzma-dev
	```

	```shell
	sudo apt install python3-openssl python3-pip
	```

	```shell
	sudo apt install python-is-python3
	```

* **update** pip and wheel:

	```shell
	pip install -U pip wheel
	```

### **pyenv** `(server)` 🐍🌐🐍🌐 


* **clone** the `pyenv` repo into `~/.pyenv`

	```shell
	git clone https://github.com/pyenv/pyenv.git ~/.pyenv
	```

    * check out the <a href='https://github.com/pyenv/pyenv#automatic-installer'>***pyenv repo***</a> for more info, and definitely the best instructions for installing it. 

* restart your shell

	```shell
	exec "$SHELL"
	```

* install the `pyenv` plugin; <a href="https://github.com/pyenv/pyenv-virtualenv">`virtualenv`</a>

	```shell
	git clone https://github.com/pyenv/pyenv-virtualenv.git $(pyenv root)/plugins/pyenv-virtualenv
	```

* restart your shell again

	```shell
	exec "$SHELL"
	```

## **Docker** `(server)` 🐳

* **install** 

	```shell
	sudo apt install docker.io
	```

* **add** `$USER` to *docker group*

	```shell
	sudo usermod -aG docker $USER
	```

* update current sesion to new group

	```shell
	newgrp docker  
	```


## **Kubernetes** `(server)` 🐙

* install minikube

	```shell
	curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube_latest_amd64.deb
	sudo dpkg -i minikube_latest_amd64.deb
	```


## **Ansible** `(server)` 🤖

* install ansible

	```shell
	sudo apt install ansible
	```

* install ansible-lint

	```shell
	sudo apt install ansible-lint
	```

* install ansible galaxy

	```shell
	sudo apt install ansible-galaxy
	```
