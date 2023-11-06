# DevOps Project Documentation

## Docker

### 1-2 Why do we need a multistage build? And explain each step of this Dockerfile.

A multi-stage build is used to optimize Docker images by separating the build process from the runtime environment. In this Dockerfile:

**Build Stage:**
1. A Maven-based image is used to build the application.
2. The pom.xml and source code are copied into the container.
3. Maven is used to compile and package the application, producing a JAR file.

**Run Stage:**
4. An Amazon Corretto 17 image is used for the runtime.
5. The JAR file built in the previous stage is copied to the runtime image.
6. The entry point is set to run the Java application from the JAR file.

This separation reduces the final image size and enhances security by excluding unnecessary build tools and artifacts in the runtime image.

**Why should we run the container with a flag -e to give the environment variables?**

Using the -e option to set environment variables when running a Docker container is essential for configuring the application in a flexible, secure, and portable way. This separates configuration from application data, promotes consistency between environments, and follows DevOps best practices.

**Why do we need a volume to be attached to our PostgreSQL container?**

We need a volume linked to the PostgreSQL container to ensure that data is preserved even when the container is destroyed.

### 1-3 Document docker-compose most important commands.

- `docker-compose up`:
  - **Description:** Start containers defined in the docker-compose.yml file.
  - **Usage:** `docker-compose up`

- `docker-compose restart`:
  - **Description:** Restart containers defined in the docker-compose.yml file.
  - **Usage:** `docker-compose restart`

- `docker-compose down`:
  - **Description:** Stop and remove containers defined in the docker-compose.yml file.
  - **Usage:** `docker-compose down`

- `docker-compose pull`:
  - **Description:** Pull service images defined in the docker-compose.yml file without starting containers.
  - **Usage:** `docker-compose pull`

- `docker-compose ps`:
  - **Description:** List containers associated with the project defined in the docker-compose.yml file.
  - **Usage:** `docker-compose ps`

- `docker-compose up -d`:
  - **Description:** Start containers in detached mode, running them in the background.
  - **Usage:** `docker-compose up -d`

- `docker-compose logs`:
  - **Description:** View the logs of the containers.
  - **Usage:** `docker-compose logs`

- `docker-compose -f [file]`:
  - **Description:** Specify an alternative Compose file (other than the default docker-compose.yml) to use.
  - **Usage:** `docker-compose -f [file] [command]`

- `docker-compose exec`:
  - **Description:** Run a command in a running container.
  - **Usage:** `docker-compose exec [service] [command]`

- `docker-compose build`:
  - **Description:** Build or rebuild services defined in the docker-compose.yml file.
  - **Usage:** `docker-compose build`

### 1-4 Document your docker-compose file.

**Version:** The Docker Compose file is defined using version 3.7.

**Services:** This section defines the individual components of the application as services.

- **backend:**
  - This service is responsible for the backend application.
  - It is built from the source code located in the `./backend/simpleapi/simple-api-student` directory.
  - The `container_name` specifies the name of the container as "backend."
  - It is part of the `app-network` to facilitate communication with other services.
  - It depends on the database service, ensuring that the database is up and running before the backend starts.

- **database:**
  - This service represents the database component of the application.
  - The `container_name` sets the name to "database."
  - The service is built from the `./database` directory.
  - It also belongs to the `app-network` for inter-service communication.

- **httpd:**
  - This service represents an HTTP server (e.g., Apache HTTP server).
  - The container is built from the `./http` directory.
  - Port mapping is defined, with host port 8081 mapped to container port 80.
  - It's part of the `app-network` for network communication.
  - The `depends_on` section ensures that the httpd service starts only after the backend service is up and running.

**Networks:**

- The `app-network` is a custom Docker network created to allow the services to communicate with each other. It provides isolation and connectivity for the services within the application.

### 1-5 Document your publication commands and published images in Docker Hub.

- `docker tag my-database USERNAME/my-database:1.0`:
  - Tags an existing Docker image as `USERNAME/my-database:1.0`.

- `docker push USERNAME/my-database:1.0`:
  - Pushes the tagged image to Docker Hub for publication.

These commands are used to tag and publish Docker images to my Docker Hub repository.

## GitHub Actions

**What is it supposed to do?** - `mvn clean verify`

The command `mvn clean verify` is used to build and run tests in a Maven-based Java project. It cleans the project, compiles the code, and executes tests to ensure the application functions correctly. It should be run from the project's root directory where the `pom.xml` file is located.

### 2-1 What are testcontainers?

Testcontainers is a Java library used for simplified integration testing by managing lightweight, disposable containers. It's particularly useful for starting and stopping services like databases during testing, ensuring test reliability.

### 2-2 Document your GitHub Actions configurations.

**GitHub Actions configuration summary:**

- **Workflow name:** "CI devops 2023"
- **Triggered by:** Pushes to the master branch and pull requests.
- **Job:** "test-backend" on `ubuntu-22.04`.
- **Steps:** Check out code, set up JDK 17, build and test with `mvn clean verify`.
- **Secrets:** `DOCKERHUB_USERNAME` and `DOCKERHUB_TOKEN` for secure access.
- Integration testing and possible CI/CD pipeline.

These configurations automate building, testing, and potentially deploying my application while safeguarding secrets for security.

**For what purpose do we need to push Docker images?**

Docker images are pushed for distribution, deployment, version control, and portability. They facilitate collaboration, support CI/CD, and ensure that the latest application version is available for use.

**Document your quality gate configuration.**

Quality gate configuration using SonarCloud involves:

- Register on SonarCloud and create an organization.
- Retrieve project and organization keys.
- Modify my GitHub Actions workflow to run SonarCloud analysis after building and testing.
- Set parameters like `sonar.projectKey` and `sonar.organization`.
- Use the `secrets.SONAR_TOKEN` for authentication.
- Ensure code quality and security with each commit and view SonarCloud analysis reports online.

## Ansible

### 3-1 Document your inventory and base commands

- The inventory file, `setup.yml`, is essential for defining target hosts and their configurations.

- In the `[all]` section, we set common variables, such as `ansible_user` (default SSH user) and `ansible_ssh_private_key_file` (SSH private key).

**Base Commands:**

- **ansible Command:**
  - Use the ansible command to interact with remote hosts:
  - `ansible all -i inventories/setup.yml -m ping`
  - This command pings all hosts for connectivity testing.

- **Gather Facts:**
  - Use the setup module to gather facts about the remote hosts, specifically the OS distribution:
  - `ansible all -i inventories/setup.yml -m setup -a "filter=ansible_distribution*"`

### 3-2 Document your playbook

**Playbook Documentation**

**Playbook (playbook.yml):**

- The playbook, `playbook.yml`, is the central orchestration file for my Ansible automation tasks.

- The `hosts: all` section specifies the target hosts to which these roles will be applied, in this case, all hosts.

- `gather_facts: false` is used to disable automatic fact-gathering, typically enabled by default.

- `become: true` indicates that Ansible should use elevated privileges when necessary.

**Roles:**

You've organized my tasks into specific roles to modularize my automation. Here are the roles I used:

- `docker`: Manages the installation of Docker on my hosts.

- `create_network`: Likely handles the creation and configuration of Docker networks for my application.

- `launch_backend`: Manages the launch of the backend component of my application, possibly a web server or application server.

- `launch_database`: Handles the deployment and setup of my database server.

- `launch_httpd`: Likely focuses on setting up and configuring the Apache HTTP Server for my application.

**Usage:**

To execute this playbook, we run the following command:

```shell
ansible-playbook -i inventories/setup.yml playbook.yml
```  

This command applies the defined roles to the specified hosts. The --syntax-check option is used to verify the syntax of the playbook before running it.

### Ansible Playbook Docker Container Tasks Configuration

This section documents the configuration for the `docker_container` tasks in the Ansible playbook.

#### Docker Tasks:

1. **Install device-mapper-persistent-data:**
   - This task uses the `yum` module to install the "device-mapper-persistent-data" package with the latest version available.

2. **Install lvm2:**
   - Similar to the previous task, it uses the `yum` module to install the "lvm2" package with the latest version.

3. **Add repo docker:**
   - This task utilizes the `command` module to execute a command that adds the Docker repository to my CentOS system using `yum-config-manager`.

4. **Install Docker:**
   - The `yum` module is used to install the "docker-ce" package, ensuring it is in a present state on my system.

5. **Make sure Docker is running:**
   - This task ensures that the Docker service is running by using the `service` module, specifying the service name as "docker" and the state as "started." Additionally, it is tagged with "docker" for future reference.

#### create_network Tasks:

1. **Create Docker Network:**
   - This task employs the `docker_network` module to create a Docker network named "app-network" with the "bridge" driver. The result is registered in the `network_info` variable for future use.

2. **Debug Network Info:**
   - To assist with debugging, this task uses the `debug` module to display the contents of the `network_info` variable.

#### launch_backend Tasks:

1. **Run the Backend:**
   - This task leverages the `docker_container` module to run a Docker container named "backend" with the specified image "matfeex/tp-devops-simple-api-backend:latest." It is connected to the "app-network" for network communication.

#### launch_database Tasks:

1. **Start the Database Service:**
   - This task uses the `docker_container` module to initiate a Docker container named "database" using the image "matfeex/tp-devops-simple-api-database:latest." It is also connected to the "app-network."

#### launch_httpd Tasks:

1. **Run the HTTPD Container:**
   - This task employs the `docker_container` module to create a Docker container named "httpd" with the image "matfeex/tp-devops-simple-api-httpd:latest." It exposes port "80" on the host and connects to the "app-network" for network communication.

These tasks represent the configuration for each role in my Ansible playbook and are responsible for setting up and managing Docker containers for my application. If you have any specific questions or need further details, please feel free to ask.
