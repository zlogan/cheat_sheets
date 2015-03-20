# SSH

## Generating SSH-Keys

### Check for SSH-Keys
- ```ls -al ~/.ssh```

### Genarte a new SSH-Key
- ```ssh-keygen -t rsa -C "your_email@example.com"```
- strongly recommed to use a good password...i leave it empty...
	- **faster** in deployment; no password input
- Add your key to ssh-agent
  - ```ssh-add ~/.ssh/id_rsa``` or only ```ssh-add```

### Add your Key to GitHub or allow login on server
- ```pbcopy < ~/.ssh/id_rsa.pub```
- GitHub
	- add this key in your GitHub-User Preferences
- Server
	- ```/etc/sshd_config```:
```
RSAAuthentication yes
PubkeyAuthentication yes
PasswordAuthentication yes
AllowAgentForwarding yes
X11Forwarding yes
AcceptEnv LANG LC_*
```
	- add your own key in the ```~/.ssh/authorized_keys2``` of the user you wish to login to with your SSH-key



## Test your SSH-Key
- ```ssh -T git@github.com```
- => Hi **username**! You've successfully authenticated, but...

## Enable Agent Forwarding
- ```touch ~/.ssh/config```
```
Host example.com
	ForwardAgent yes
```
- ```Host *``` apply settings to all hosts

## Test Agent Forwarding
- SSH into your server and run ```ssh -T git@github.com```
- Inspect ```SSH_AUTH_SOCK```
- ```echo $SSH_AUTH_LOCK```
- => /tmp/ssh-4haeonuhaeoau/agent.79453

## Troubleshooting Agent Forwarding
- your SSH-Key must work locally
- your System must allow forwarding
- check order of config-files by ```ssh -v example.com```
- your server must allow agent forwarding and incoming connections
	- ```/etc/sshd_config``` - ```AllowAgentForwarding```
- your local ssh-agent must be running
	- check this with ```echo $SSH_AUTH_LOCK``` locally
- **your key must be available to ssh-agent:** ```ssh-add -L```
	- if not ```ssh-add yourkey``` or only ```ssh-add```
