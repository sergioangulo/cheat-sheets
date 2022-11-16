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

## Oracle get limited random rows from table
``` sql
SELECT *
FROM   (
    SELECT *
    FROM   MY_CUSTOM_TABLE
    ORDER BY DBMS_RANDOM.RANDOM)
WHERE  rownum < 21;
```
## Activate autocompletion Jupyterlab

``` bash
pip install jupyter_contrib_nbextensions
jupyter contrib nbextension install --user
jupyter nbextensions_configurator enable --user
```



TDD in python: continuosly run tests
------------------------------------
```bash
$ pip install pytest-watch
```

```bash
$ cd myproject
$ ptw
 * Watching /path/to/myproject
```

ptw exits in some cases. We can use nodemon to restart it:

```bash
nodemon -e py --exec ptw
```
The project structure must be:

```bash
└base
├── conftest.py
├── moduleA
│   ├── __init__.py
│   └── moduleA.py
└── tests
    └── test_module.py
```
inside test module 
```bash
from base.moduleA.moduleA import someClass
```

In windows when exists troubles:
```bash
FOR /L %N IN () DO (@pytest & @timeout 5)
```

## Running anaconda console in cmd windows
1. Search your anaconda instalation dir: (ex: C:\user\Anaconda3)
3. Add to Path: (edit system environment vars - env vars - click on Path - edit - new )
    - C:\user\Anaconda3
    - C:\user\Anaconda3\Scripts
4. Start a cmd and run: **conda init**
5. restart cmd
6. Voilá! Now you can use **conda activate my-env**


## Select a python virtual environment in visual studio code
1. ctrl + shift + P
2. search: **Python: Select Interpreter**
3. Choose your environment
4. Ready to code!
