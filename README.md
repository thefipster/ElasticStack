# Intracon Logging with the Elastic Stack

This repository contains a vagrant environment to setup a local Elastic Stack inside a HyperV Virtual Machine.

## Dependencies

* `Hyper-V Platform` Windows Feature needs to be enabled
  * including `Hyper-V Module for Windows Powershell`
* Vagrant (<https://www.vagrantup.com/>)

## Getting Started

Clone the repository and navigate into the repo folder.

Open an administrative console and enter:

```bash
vagrant up --provider=hyperv
```

Vagrant will now start to download the base image and provision the virtual machine. At some point Vagrant tries to map the local repository folder to the VM. This is done via SMB and requires your Windows Credentials so that the VM has access to the local filesystem. When asked please supply your credentials as states below:

```bash
==> default: Machine booted and ready!
==> default: Preparing SMB shared folders...
    default: You will be asked for the username and password to use for the SMB
    default: folders shortly. Please use the proper username/password of your
    default: account.
    default:
    default: Username: ENTER YOUR WINDOWS USERNAME WITHOUT DOMAIN
    default: Password (will be hidden): ENTER YOUR WINDOWS PASSWORD
```

if everything was entered correctly it should continue like this:

```bash
Vagrant requires administrator access to create SMB shares and
may request access to complete setup of configured shares.

==> default: Setting hostname...
==> default: Mounting SMB shared folders...
    default: YOUR_REPO_LOCATION => /vagrant
```

If everything finished correctly you should see these lines:

```bash
==> default: Running provisioner: shell...
    default: Running: inline script
    default: vm.max_map_count = 262144
```

and have an active prompt on the console. 

## Accessing the box

Open up Hyper-V manager and checkout the IP address of the machine `intraconlogging_default_*`

* Kibana Interface: http://HYPER-V-MACHINE-IP
* Logging Endpoint: http://HYPER-V-MACHINE-IP:9200
* Administration: http://HYPER-V-MACHINE-IP:9000

You can also add an entry to the hosts file like:

```
HYPER-V-MACHINE-IP  logbox
```

then the above URLs look like

* Kibana Interface: http://logbox
* Logging Endpoint: http://logbox:9200
* Administration: http://logbox:9000

## Basic machine control

```bash
# First time it provisions the machine
# otherwise it just starts it.
vagrant up

# Prints the current state of the vm.
vagrant status

# Shutting down the machine.
vagrant halt

# Deleting the machine.
vagrant destroy

# Reprovision the machine aka run the provision script again.
vagrant provision

# SSH into the machine
vagrant ssh
```

## Starting over

Something went wrong or the box won't behave as expected due to reasons.

Open a console and type
```
vagrant destroy
```

and confirm with "y"

```
    default: Are you sure you want to destroy the 'default' VM? [y/N] y
==> default: Stopping the machine...
==> default: Deleting the machine...
```

Start fresh with "Getting started" section.

## Kibana Access

Kibana is protected with a nginx reverse proxy. The credentials currently default to:
> hcvision : awesomelogging

If you want to change the credentials use the following tool to create a `htpasswd` file.
> http://www.htaccesstools.com/htpasswd-generator/

Then update the `htpasswd.users` file inside the `nginx` folder with the generated content.

## Known Issues

### Restart

After a restart nginx web server providing the kibana UI will not start automatically for some reason, even though it is configured to do so. 

#### Portainer Workarount

* Open the portainer administration `http://logbox:9000`
  * The first time you need to set a username and password and select "local".
* Navigate to local server -> containers
* Start the container with the name: `vagrant_proxy_1 `

#### SSH Workaround

* Navigate to the repo folder
* `vagrant ssh`
* `sudo docker ps -a`

> **e9666db46846** nginx:latest "nginx -g 'daemon of…" 16 minutes ago Exited (127) 13 minutes ago   0.0.0.0:80->80/tcp  vagrant_proxy_1

* locate the ID of the nginx container, in this case e9666db46846
* `sudo docker start e9666db46846`