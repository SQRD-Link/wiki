# 1 configure package manager

````
vi /etc/dnf/dnf.conf
````
add: 
````
max_parallel_downloads=10
fastestmirror=True
````

# 2 update package manager

````
dnf update -y
````

# 3 install open SSH server

````
dnf install openssh-server -y
````

# 4 configure ssh access

````
vi /etc/ssh/sshd_config
````

````
service sshd restart
````
