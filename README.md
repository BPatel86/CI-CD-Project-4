**🚀 Simple DevOps Project - 4**

This project demonstrates how to use **Git**, **Jenkins**, **Ansible**, **DockerHub**, and **Docker** to automate application deployment into Docker containers. The complete pipeline involves building Docker images, pushing them to DockerHub, and running them on a Docker host—all triggered from Jenkins using Ansible playbooks.


**🔧 Prerequisites**

Ensure the following are set up before running the project:

- Jenkins installed and configured
- Ansible installed and configured
- Docker installed on the Ansible client
- DockerHub account with login access from the Ansible server

**🔄 CI/CD Flow**

This is the continuous integration and deployment (CI/CD) flow implemented in this project:

           +------------+               +-------------+               +----------------+
           |   GitHub   |  ---> Push    |   Jenkins   |  ---> Trigger |   Ansible       |
           +------------+               +-------------+               +--------+-------+
                                                                          |
                                                                  Run Playbooks
                                                                          |
                                                                          v
                   +------------------+     Push     +----------------+     Deploy     +----------------+
                   | Build Docker Img |   -------->  |  DockerHub     |  -------->     |  Docker Host    |
                   +------------------+              +----------------+                 +----------------+

**Code Push (GitHub) → Triggers Jenkins Job**

**Jenkins:**

Runs Ansible Playbooks
Builds Docker image
Tags with BUILD_ID or version
Pushes to DockerHub

**Ansible:**

Connects to Docker Host
Deploys container using image from DockerHub

**Docker Host:**

Runs the container, making the application accessible

**✅ Part 01: Build and Push Docker Image**

In this step, we automate the creation of a **Docker image using Jenkins and Ansible, and push it to DockerHub.**

**Steps:**

- Log in to Jenkins.
- Create a Jenkins job (freestyle or pipeline).
- SSH into the Docker host and verify that no images or containers exist yet.
- Log in to DockerHub and confirm that the image (e.g., binal86_demo) is not present.
- Execute the Jenkins job.
- Re-check DockerHub — the new image appeared.

**🔧 Troubleshooting:**

- Docker must be installed on the Ansible server.
- Ensure you're logged in to DockerHub on the Ansible server using
- Make sure the Jenkins/Ansible user is part of the docker group

**✅ Part 02: Deploy Containers**

This step focuses on creating a Docker container from the previously built image using Ansible, initiated by Jenkins.

**Steps:**

- Write a playbook named create_docker_container.yml to deploy a container.
- Add the playbook execution to the Jenkins job.
- Run the Jenkins job.
- Verify that the container is up and running on the Docker host.
- You should be able to access the application via Web browser on port 8090.

**🔐 Note:**

- Ensure that port 8090 is open in your AWS Security Group (or firewall) if using a cloud-based VM.

**✅ Part 03: Deploy with Version Control**

In this part, we improve the deployment process by tagging Docker images with version numbers using Jenkins environment variables.

Why?
Relying on the latest tag can lead to unpredictable behavior. Maintaining image versions allows rollback and better traceability.

- Jenkins Environment Variables Used:
```
    BUILD_ID – Auto-incremented build ID
    JOB_NAME – Jenkins job name
```
**Steps:**

Modify the Jenkins job from Part-01:

- Use the BUILD_ID in your Docker image tagging:
```
docker build -t myapp:$BUILD_ID .
docker tag myapp:$BUILD_ID <dockerhub-username>/myapp:$BUILD_ID
docker push <dockerhub-username>/myapp:$BUILD_ID
```
- Update the Ansible playbook in Part-02 to deploy using the versioned image:
```
image: "<dockerhub-username>/myapp:{{ lookup('env','BUILD_ID') }}"
```
**🚑 Troubleshooting**

- Make sure Jenkins is connected to Ansible and Docker servers via SSH or agent nodes.
- Ensure DockerHub credentials are valid and stored securely (consider Jenkins Credentials Manager).
- Open required ports in cloud firewalls or security groups (e.g., AWS SG).

**🛠️ Tools Used**

- Git – Source code version control
- Jenkins – Continuous Integration / Continuous Deployment
- Ansible – Configuration Management and Automation
- Docker – Containerization
- DockerHub – Image registry

**📌 Project Outcome**

By the end of this project, we will have:

- Automated the build, tag, and push of Docker images using Jenkins and Ansible
- Created containers from DockerHub images on a remote host
- Implemented version control on Docker images for rollback safety


