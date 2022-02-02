# cheat-sheets
Cheat Sheets to fast  access

## create a user in ubuntu
useradd -m sergio
### create a password
passwd sergio
### add to sudoers
usermod -aG sudo sergio
### change terminal to bash
edit /etc/passwd
change /bin/sh to /bin/bash

## Run docker with non root user
### Create docker group
sudo groupadd docker
### Add your user to the group
sudo usermod -aG docker sergio
### Activates changes to groups or Logout/Login
newgrp docker
Or Well ... logout then login and enjoy

## Run a script like a daemon
nohup Script source & 

## Create a zero-file with an specific size (windows)
//on Windows
fsutil file createnew 1MB.dummy 1048576 
fsutil file createnew testFile_fsutil <1Tb>   
//(on Linux)
dd if=/dev/zero of=testFile_dd bs=1024M count=1024  
