sudo dnf --refresh update

sudo dnf upgrade

sudo dnf install yum-utils

sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

sudo dnf install docker-ce docker-ce-cli containerd.io docker-compose-plugin -y

sudo systemctl enable docker

sudo systemctl start docker

sudo yum install python3-pip -y

pip install docker-compose

test


