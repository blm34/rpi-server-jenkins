# Jenkins

Docker compose configuration for runnign Jenkins on ubuntu server

## Requirements

* Docker
* Docker compose
* An Nginx reverse proxy running as configured [here](https://github.com/blm34/self-hosted-nginx)

## Set Up

Clone this repository

```bash
git clone git@github.com:blm34/rpi-server-pihole.git
```

Copy the file `.env.template` to `.env` and fill in the variables. Ignore
`JENKINS_AGENT_SSH_PUBKEY` for now.

```bash
cp .env.template .env
```

Start the container with

```bash
docker compose up -d
```

Go to the web UI at `<ip-address>:8080` which will ask for the administrator
password. This can be found in the docker logs as follows

```bash
# Get the Jenkins admin password
docker logs jenkins | less
```

Enter the password and click continue. On the next page, select ‘Install Suggested Plug-ins’.

Once the plugins have installed create an admin user. Use ‘admin’ as the username,
and select a good password. Full name can be ‘Administrator’ and use your email address.
Set the Jenkins URL to whatever you will use to access it.

## Add a Jenkins Agent

Create an SSH key pair to allow the controller to access the agent via SSH

```bash
ssh-keygen -f jenkins-agent
```
In the Jenkins webpage, go to 'settings' then 'credentials'. Under 'Stores
scoped to Jenkins' click on 'System'. Next click 'Global credentials
(unrestricted)', then 'add some credentials'. Select to use SSH with a private
key, and click create. Click the settings of the new item. Limit the scope to
'System', set the username to 'Jenkins', and enter the private key just created.
Add the public key as 'JENKINS_AGENT_SSH_PUBKEY' in the .env file.

Stop and restart the container

```bash
docker compose down

docker compose up -d
```

Go back to the Jenkins webpage and refresh it. Log in using the username 'admin'
and the password just created.

Go back to settings, click 'Nodes', then 'New Node'. Give it the name 'jenkins
agent'. Set the remote root directory to '/home/jenkins/agent'. Select 'Use
this node as much as possible'. Set the launch method to 'Launch agents via SSH',
and set the host to 'jenkins agent'. Select the credentials just defined. For
verification strategy, select 'Non verifying Verification Strategy'.

