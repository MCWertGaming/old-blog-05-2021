+++
title = "Setting up step-ca with docker on CentOS"
date = "2021-05-26"
author = "Damon Leven"
description = "This post describes the process of installing and testing a local step-ca installation using docker on CentOS 7"
+++

In this small blog post, I'll explain how I'm running my personal Certification Authority (CA) at home for securing my local we traffic with automated encryption.

[German version](https://xx) (soon)

# Why Smallstep certification authority?

My goal was to protect my local traffic with SSL but without having to deal with certificates. The problem with using Let's Encrypt is that it won't work without exposing the web service on port 80 / 443 to the public which I don't want to do for all of my services. Also we used Microsoft Active Directory for creating certificates at first, because my father (who operates the server at his home) requires it anyway for his work. But that was really complicated as it's not supporting ACME at all. That means that you would have to create all certificates manually. Another problem was that there is not really a good way to do order certificates with a linux host. That would have meant, that I'd need to create the certification request on my destination server, then copy it to a Windows machine, then create the certificate, then copy it back and finally I'm able to use it on my service. That's unnecessary work if you ask me! So I searched for a way to completely automate the certification process. The most popular protocol for that is probably ACME, which offers ACME compatible clients to order certificates themselves without requiring any interaction (except some small bits of configuration of course).

If you are searching for self-hosted open-source certification authorities on GitHub, you will probably find these two projects:
- [smallstep/certificates](https://github.com/smallstep/certificates)
- [letsencrypt/boulder](https://github.com/letsencrypt/boulder)

I actually didn't even knew before that let's encrypt is open-source! In my opinion it's really nice, but sadly there is not that much documentation on it...
Going with that, stepstone's solution comes with a big and detailed documentation. Also there are many other resources on setting it up and using it. 
Another cool thing is that they provide good examples on using their solution with the most popular ACME clients and reverse-proxies, which was helping me a lot.

# Why CentOS?

CentOS is in my opinion the best operating system for servers, because it's secure and there are no issues if you decide to just let it automatically update itself without having to do it manually or checking it's up-to-date state. I'm using the CentOS 7 LXC provided by my Proxmox VE server, because LXC's are using way less resources than VMs. To run docker, you have to enable nesting in your proxmox configuration and I personally would strongly suggest to make sure the LXC is unprivileged (note that this is not required, if you are running CentOS 7 in a VM).
I also recommend using CentOS 7 because CentOS 8 is only running until the end of the year and the docker support is discontinued.

Also note that CentOS 7 is only supported until 2024 and it's possible, that RedHat is cutting down it's support like they did with CentOS 8. I wouldn't recommend CentOS stream, as it's not as secure and stable as the "normal" CentOS versions used to be. Please see [RockyLinux](https://rockylinux.org/) (currently in pre-release), [AlmaLinux](https://almalinux.org/) and [CloudLinux](https://www.cloudlinux.com/) for good replacements. The installation and usage will be mostly (if not completely) the same.

# Installing docker
To install docker, You only need to add the official docker repository to your Package Manager many and you are ready to go:

```bash
# remove conflicting packages
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
# install yum-utils for the yum-config-manager
sudo yum install -y yum-utils
# install the docker ce repository
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
# finally install docker
sudo yum install docker-ce docker-ce-cli containerd.io
```
> *[Taken from the official docker documentation.](https://docs.docker.com/engine/install/centos/)*

And you are ready to go!

# Running the Authentication Authority with docker
Smallstep is providing their ca on DockerHub [smallstep/step-ca
](https://hub.docker.com/r/smallstep/step-ca). You will find more information there. The official dockerfile can be found [here](https://github.com/smallstep/certificates/blob/master/docker/Dockerfile.step-ca).

## prepare the certification authority
```bash
# pull the latest docker image
docker pull smallstep/step-ca
# create a volume to store persistent data outside of the container
docker volume create step
# start a shell inside of the container
docker run -it -v step:/home/step smallstep/step-ca sh
```
Now you should have a shell inside of the step-ca container open. We have to prepare the volume before we can use our ca. For that we have the following command:
```bash
step ca init
```
This basically asks you a few questions about the purpose of the ca (which will be stored inside of the 'root certificate') and create your root certificate (which is required for signing the ones of your services) and creates the required configuration files. The output should look something like this:
```
$ step ca init
✔ What would you like to name your new PKI?: Smallstep
✔ What DNS names or IP addresses would you like to add to your new CA? localhost
✔ What address will your new CA listen at?: :9000
✔ What would you like to name the first provisioner for your new CA?: admin
✔ What do you want your password to be?: <your password here>

Generating root certificate...
all done!

Generating intermediate certificate...
all done!

✔ Root certificate: /home/step/certs/root_ca.crt
✔ Root private key: /home/step/secrets/root_ca_key
✔ Root fingerprint: f9e45ae9ec5d42d702ce39fd9f3125372ce54d0b29a5ff3016b31d9b887a61a4
✔ Intermediate certificate: /home/step/certs/intermediate_ca.crt
✔ Intermediate private key: /home/step/secrets/intermediate_ca_key
✔ Default configuration: /home/step/config/defaults.json
✔ Certificate Authority configuration: /home/step/config/ca.json

Your PKI is ready to go. To generate certificates for individual services see 'step help ca`}
```
> *[Taken from the official docker container page on DockerHub](https://hub.docker.com/r/smallstep/step-ca)*

Please store your password somewhere secure and note the fingerprint (does not have to be secure) for later usage.
Now we have to store the password somewhere for the container. The password is basically used for encrypting the private key for security reasons. That means that the Key file can only be used with the given password. The official way is to just store the password inside of `/home/step/secrets/password` as unencrypted string. 
> Note that this method is not secure in any way and should only be used for testing or local use. If you want to do it really secure, smallstep recommends to use an hardware encrypted device for storing and accessing the private key (yubikey for example, currently 45$ on amazon). [An Really good walk through on how to set it up with the Raspberry pi 4 is available on their blog](https://smallstep.com/blog/build-a-tiny-ca-with-raspberry-pi-yubikey/).

To do that, you basically just have to do:
```
vi /home/step/secrets/password
```
and paste your password in there.
If you are unfamiliar with vi, you just can install nano with `apk add nano` or look at these instructions:
- press CRTL+O to enter the write mode
- paste your password
- if the first characters get removed by what ever goes wrong here just add some new lines, paste it and then remove them again
- press ESC to exit write mode
- Press :wq to write the changes to the file system and quit vi

Now the CA is ready to use!

## adding ACME support
Step-ca is supporting ACME out-of-the-box, but it's not enabled by default. To enable it, just add the ACME provisioner to your configuration:
```bash
step ca provisioner add acme --type ACME
```

And we are done! Now you can exit the container with `exit`.

## run the ca for the first time:
```bash
docker run -d -p 127.0.0.1:9000:9000 -v step:/home/step smallstep/step-ca
```

This starts the ca in the background on port 9000. If you would rather have the ca on port 443 (so you don't have to specify the port in the URL) just change the port
```bash
docker run -d -p 127.0.0.1:443:9000 -v step:/home/step smallstep/step-ca
```

To use the ca in your local network, you have to change the port binding to your servers IP. This is an example for running it on the ip 10.10.10.40 on port 443:
```bash
docker run -d -p 10.10.10.44:443:9000 -v step:/home/step smallstep/step-ca
```

Now you can actually use your ca!

## making the ca "autostart"
There are quiet a few ways to autostart containers with docker. I usually run them with systemd, as it's also used for the system's services.

This is an example systemd unit file:
```
# step-ca sample systemd unit
# located in /usr/local/lib/systemd/system/step-ca.service
[Unit]
Description=Smallstep certification authority
Requires=docker.service
After=docker.service
    
[Service]
Restart=always
ExecStartPre=-/usr/bin/docker kill step-ca
ExecStartPre=-/usr/bin/docker rm step-ca
ExecStartPre=-/usr/bin/docker pull smallstep/step-ca
ExecStart=/usr/bin/docker run --name step-ca -p 10.10.10.40:443:443 -v step:/home/step smallstep/step-ca
ExecStop=/usr/bin/docker stop step-ca
    
[Install]
WantedBy=multi-user.target
```
Now you just need to start and enable your newly created service:
```bash
sudo systemctl start step-ca
sudo systemctl enable step-ca
```

# Testing the CA for functionality
Now, after our ca is finally running, we just have to check if it's working correctly. In the same process, we are also making the `step` utility ready to be used on your local machine to interact with the ca.

## installing the step cli
There are some distributions, that are packaging it already, but sadly not all.

On Archlinux and Archlinux based distributions:
```bash
sudo pacman -Syu step-cli
```
> Please note that you need to use the `step-cli` command instead of `step` on Archlinux!
> This is because `step` is already a packaged binary

On MacOS using brew:
```bash
brew install step
```

On Debian:
```bash
VERSION=0.15.16
wget https://github.com/smallstep/cli/releases/download/v${VERSION}/step-cli_${VERSION}_amd64.deb
sudo dpkg -i step-cli_${VERSION}_amd64.deb
```
> Please note to change the VERSION variable to the [newest one on github](https://github.com/smallstep/cli/releases/latest)
 
On Alpine Linux:
```bash
# add the testing repository, as step is not yet in the stable ones
echo "http://dl-cdn.alpinelinux.org/alpine/edge/testing" >> /etc/apk/repositories
# finally install it using the apk package manager
apk add step-cli
```

On NixOS:
> The package is located [here](https://search.nixos.org/packages?channel=20.09&show=step-cli&from=0&size=50&sort=relevance&query=step-cli), you might have to add the testing repository first, but I think that you know hoe to install packages if you are using NixOS :)
 
On other Linux distributions:
```bash
VERSION=0.15.16
wget -O step.tar.gz https://github.com/smallstep/cli/releases/download/v${VERSION}/step_linux_${VERSION}_amd64.tar.gz
tar -xf step.tar.gz
sudo cp step_${VERSION}/bin/step /usr/bin
```
> Please note to change the VERSION variable to the [newest one on github](https://github.com/smallstep/cli/releases/latest)

To check if the installed package works, just print out the version:
```bash
step --version
```
> Note that some systems you need to run `step-cli --version`

## Actually testing your CA
The first thing we will do is bootstraping our ca. It means that we download our ca's root certificate validating it with our ca's fingerprint and storing a configuration file for the step util for saving our configuration.
```bash
step ca bootstrap --ca-url https://ca.example.org \
  --fingerprint <fingerprint> \
  --install # optional, should install the root cert on your system, but does not work for me on Archlinux, CentOS and alpine
```

To install the certificate into your systems trust store, you have to check your systems documentation as it can vary a lot between different systems.

finally you can test it:
```bash
curl ca.example:port/health
```
If it's successful, you should see the following:
```
$ curl ca.example:port/health
{"status":"ok"}
```

And now you are done! See my other posts about what and how to do after finishing setting up the ca:
- Setting up Caddy reverse proxy with local CA (coming soon)
- Setting up Proxmox with local CA (coming soon)
- Setting up Adguard with local CA (coming soon)
- Exposing docker container with Caddy and local CA (coming soon)
- Exposing services on multiple servers securely to the web (coming soon)

#### Happy coding!

Sources of used software and assets on this site can be found [here](/about/#software-used-on-this-site).
