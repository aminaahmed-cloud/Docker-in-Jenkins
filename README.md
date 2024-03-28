# Docker Setup in Jenkins

## Introduction
This guide outlines the steps to integrate Docker with Jenkins for building and deploying applications. It includes setting up Docker within Jenkins, configuring permissions, building Docker images, pushing them to Docker Hub, and pushing images to a Nexus repository.

## Setting up Docker in Jenkins

1. **Start Jenkins Container with Additional Volumes**:
    ```bash
    docker run -d -p 8080:8080 -p 50000:50000 \
    -v jenkins-home:/var/jenkins-home \
    -v /var/run/docker.sock:/var/run/docker.sock jenkins/jenkins:lts
    ```

<img src="https://i.imgur.com/1EwOfGv.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

*****


2. **Install Docker Inside Jenkins Container**:
    - Enter the container as root:
        ```bash
        docker exec -u 0 -it <container_id> bash
        ```
    - Install Docker:
        ```bash
        curl https://get.docker.com/ > dockerinstall && chmod 777 dockerinstall && ./dockerinstall
        ```
    - Adjust permissions for Docker socket:
        ```bash
        chmod 666 /var/run/docker.sock
        ```

<img src="https://i.imgur.com/FsqnvO8.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

****

3. **Test Docker Installation**:
    - Switch to Jenkins user:
        ```bash
        su jenkins
        ```
    - Test Docker installation:
        ```bash
        docker pull redis
        ```

<img src="https://i.imgur.com/0zIAgC8.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

*****


## Integrating Docker with Jenkins Jobs

1. **Configure Docker Credentials**:
    - Create a Docker Hub account.
    - Configure Docker Hub credentials in Jenkins.

<img src="https://i.imgur.com/YpAMTly.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

<img src="https://i.imgur.com/WxNoL7k.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

****

2. **Build Docker Image from Git Repository**:
    - Create a Freestyle Jenkins job.
    - Add the following shell command in the build steps:
        ```bash
        docker login -t java-maven-app:1.0 .
        docker build -t java-maven-app:1.0 .
        ```
<img src="https://i.imgur.com/j5pPatA.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

<img src="https://i.imgur.com/tbMOCaT.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

<img src="https://i.imgur.com/cQyP9tJ.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

****

3. **Push Docker Image to Docker Hub**:
    - Add the following shell command in the build steps of the Jenkins job:
        ```bash
        docker push java-maven-app:1.0
        ```
<img src="https://i.imgur.com/SLua1zT.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

****

4. **Push Docker Image to Nexus Repository**:
    - Configure Nexus repository in Docker daemon configuration.
        - Create or edit `/etc/docker/daemon.json`.
        - Add:
            ```json
            {
                "insecure-registries": [ "nexus3:8083" ]
            }
            ```
    - Restart Docker daemon:
        ```bash
        systemctl restart docker
        ```
    - Reconfigure permissions for Docker socket.
    - 
<img src="https://i.imgur.com/ncBQybW.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

****

5. **Modify Jenkins Job Build Configuration**:
    - Update the shell script in the build steps of the Jenkins job to tag and push the Docker image to Nexus repository.
    - 
<img src="https://i.imgur.com/ooiET1A.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

<img src="https://i.imgur.com/Uxr1pRv.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

*****
