# vscode_remote_slurm
Helper script for executing commands before connecting to vscode remote. This can be used to run vscode remote on the compute node of a slurm cluster.  
Conditionally wraps the ssh command if `salloc` is in the RemoteCommand. Passes through otherwise.  

### How I have been able to get this working:  
- Put the ssh_wrapper.sh script somewhere.
- Make sure it's executable: `chmod +x ssh_wrapper.sh`
- Change vscode to run this instead of your default ssh binary.
- Create a host entry in your ssh_config (example below) with a RemoteCommand detailing your resources.
- Hope it works?

### TODO:  
- Wrap into extension so it runs this script on a button press instead of changing vscode to only use this script for ssh.


Notes:
I have tested this on a Mac M1 connecting to a Centos 7 Slurm Cluster. Vscode Insiders v1.83 and Remote - SSH v0.106.4.  
It hasn't been tested on anything else yet.  

These are my Remote SSH settings:
```
    "remote.SSH.connectTimeout": 60,
    "remote.SSH.logLevel": "trace",
    "remote.SSH.showLoginTerminal": true,
    "remote.SSH.path": "/path/to/ssh_wrapper.sh",
    "remote.SSH.useExecServer": true,
    "remote.SSH.maxReconnectionAttempts": 0,
    "remote.SSH.enableRemoteCommand": true,
```


Define your ssh connection in ssh_config like so with your desired slurm allocation:
```
Host remotehost
  HostName your.remote.host
  RequestTTY yes
  ForwardAgent yes
  IdentityFile /path/to/sshkey
  RemoteCommand salloc --no-shell -n 1 -c 4 -J vscode --time=1:00:00
  User remoteusername
```

Connect and hopefully it works.
