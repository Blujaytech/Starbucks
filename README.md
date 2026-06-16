Starbucks-CICD-Project
----------------------
# Project-Overview

![Starbucks Clone Deployment](https://github.com/user-attachments/assets/6b654f47-9537-4b88-9584-41c760fc49ac)

# Tech-stack

Server(Rhel9)..1 [Take high configure]

take. volume memory 70GB

# Deployment Stages:

<img width="966" alt="Screenshot 2024-09-15 at 7 20 49 AM" src="https://github.com/user-attachments/assets/ddb5e618-79ab-49b3-8f13-b5114824eec3">
   
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
```
sh docker.sh
```

```
curl -sSfL https://raw.githubusercontent.com/docker/scout-cli/main/install.sh | sh -s -- -b /usr/local/bin

```
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
du -sh /var/lib/* 2>/dev/null | sort -rh | head -10
df -h /var
vgdisplay RootVG | grep -E "Free|Size"
lvextend -L +3G /dev/mapper/RootVG-varVol
xfs_growfs /var
echo "cachedir=/var/tmp/dnf-cache" >> /etc/dnf/dnf.conf
dnf clean all
```
```
yum clean allyum makecache

dnf install java-21-openjdk java-21-openjdk-devel -y

java --version
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
# Trivy-Installation

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

```
http: <PlubicIP:8080> ---Jenkins-server
http: <PlubicIP:9000> ---SonarQube
```

# Jenkins-Plugin

```
nodejs
sonarqube scanner
sonarqube Qualitygate
OWASP
Eclips
Docker pipeline
Docker Comman
Docker
Pipeline StageView
```
