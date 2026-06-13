Starbucks-CICD-Project
----------------------

# Project-Overview

# Tech-stack

Server(Rhel9)..1 [Take high configure]

take. volume memory 70GB
   
# Installation- process.

# Docker-Installation

vi docker.sh 

```
#!/bin/bash

#check whether root user or not
R="\e[31m"
N="\e[0m"

yum install -y yum-utils
yum-config-manager --add-repo https://download.docker.com/linux/rhel/docker-ce.repo
yum install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
systemctl start docker
systemctl enable docker
usermod -aG docker ec2-user
echo -e "$R Logout and Login again $N"

```
sh docker.sh
```

```
curl -sSfL https://raw.githubusercontent.com/docker/scout-cli/main/install.sh | sh -s -- -b /usr/local/bin

```
sudo chmod 777 /var/run/docker.sock
```

# Extende Volume-Memory
```
growpart /dev/nvme0n1 4
lvextend -l +50%FREE /dev/RootVG/rootVol
lvextend -l +50%FREE /dev/RootVG/varVol
xfs_growfs /xfs_growfs /var
lsblk
```


```
docker run -d -p 9000:9000 --name sonarqube   -v sonarqube_data:/opt/sonarqube/data   -v sonarqube_logs:/opt/sonarqube/logs   -v sonarqube_extensions:/opt/sonarqube/extensions   sonarqube:lts-community
```

```
docker ps
```

```
docker logs -f sonarqube
```

# Jenkins-Installation.

```
curl -L -o /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
```

```
rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
```

```
dnf install java-21-openjdk java-21-openjdk-devel -y
```

```
yum install -y jenkins
```

```
systemctl enable jenkins
```

```
systemctl start jenkins
```

```
systemctl status jenkins
```

```
cat /var/lib/jenkins/secrets/initialAdminPassword
```

```
cat << EOF | sudo tee -a /etc/yum.repos.d/trivy.repo
[trivy]
name=Trivy repository
baseurl=https://aquasecurity.github.io/trivy-repo/rpm/releases/\$basearch/
gpgcheck=1
enabled=1
gpgkey=https://aquasecurity.github.io/trivy-repo/rpm/public.key
EOF
```

```
sudo yum -y install trivy
```
