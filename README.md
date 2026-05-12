# Jenkins CI/CD Pipeline for Dockerized Applications on AWS EC2

A DevOps project for containerizing a Python Flask application and a static website using Docker Compose, with a fully automated CI/CD pipeline using Jenkins.

The CI/CD pipeline automates deployment, testing, and security checks before deploying to an AWS EC2 instance.

This project is flexible, allowing different programming languages, tools like Podman instead of Docker, and configurations. 

The example here focuses on a Python Flask app and a static website.

## Prerequisites
Ensure you have the following installed:

- Docker (or Podman)
- Docker Compose
- Jenkins
- Python 3.x
- AWS EC2 instance with SSH access

## Dependencies
The Python app requires the following dependencies (specified in requirements.txt):

```bash
flask~=3.0.3
telebot~=0.0.5
pillow~=10.3.0
loguru~=0.7.2
matplotlib~=3.8.4
pylint
```

## Project Structure
```bash
jenkins-docker-ec2-cicd/
│   README.md
│   compose.yaml
│   build.Jenkinsfile
│   .gitignore
│   .pylintrc
│
├── polybot/
│   ├── photos/
│   ├── test/
│   ├── app.py
│   ├── bot.py
│   ├── image_proc.py
│   ├── Dockerfile
│   └── requirements.txt
│
├── nginx/
│   ├── Dockerfile
│   ├── index.html
│   └── nginx.conf
│
└── jenkins-agent/
    └── Dockerfile
```

## Setup and Installation
### 1. Clone the Repository
```bash
git clone https://github.com/libermanofri/jenkins-docker-ec2-cicd.git
cd jenkins-docker-ec2-cicd
```
### 2.Install Python dependencies:
```bash
pip install -r polybot/requirements.txt
```
### 3. Build and Run the Containers
Navigate to the project root and use Docker Compose to build and start the containers:


```bash
docker-compose up --build
```
### 4. Access the Application
The Python Flask app should be running at http://localhost:8443. 

The static website should be available at http://localhost:8444.


## Running the CI/CD Pipeline
The Jenkins pipeline automates the process of building, testing, and deploying the application.

### Jenkinsfile Structure:
- Checkout: Pulls the latest code from the repository.
- Static Code Analysis: Runs pylint to check the code quality.
- Unit Testing: Executes Python unit tests using pytest.
- Build and Deploy: Builds Docker images and deploys them using Docker Compose.

### Running the Jenkins Pipeline
#### 1. Setup Jenkins:
- Install necessary Jenkins plugins (Docker, Docker Pipeline, Warnings Next Generation (for pylint), Git, etc.).
- Configure a new pipeline job linked to this repository.
- Use the provided build.Jenkinsfile as the pipeline script.
#### 2. Configure Credentials in Jenkins:
- Add Docker Hub, Nexus, and other relevant credentials.
#### 3. Configure a webhook from GitHub to trigger builds automatically on push.
#### 4. Run the Pipeline:

The pipeline performs the following tasks:
- Builds Docker images for both the Python app and the Nginx static website.
- Runs code linting, unit tests, and security scans.
- Pushes images to Docker Hub and Nexus.
- Deploys the images to an AWS EC2 instance.

## Detailed Pipeline Stages
The Jenkins pipeline includes:

#### 1. Load Shared Library: Loads the custom shared library to enable reusable functions and utilities.
#### 2. Checkout and Git Commit Hash Extraction: Extracts the commit hash for versioning.
#### 3. Build Docker Images: Uses docker-compose.yaml to build the images.
#### 4. Install Python Requirements: Installs necessary Python packages.
#### 5. Static Code Linting and Unit Testing: Runs code analysis with pylint and unit tests with pytest.
#### 6. Security Vulnerability Scanning: Scans images using Snyk.
#### 7. Image Tagging and Pushing: Tags and pushes images to Docker Hub and Nexus.
#### 8. Deployment on EC2: Deploys the app and website to an AWS instance using SSH.
#### 9. Post Always: Executes cleanup steps and notifications, ensuring they run regardless of the pipeline’s success or failure.

## Troubleshooting
- Make sure your AWS EC2 instance has Docker installed and is accessible via SSH.
- Use docker-compose logs for debugging container issues.

## Project Environment
This project was run and tested in the following environment:

- OS: Microsoft Windows 11 Home with WSL support (Version: 2.2.4.0, Linux Distribution: Ubuntu 20.04)
- CI/CD: Jenkins
- Deployment: Docker on an AWS EC2 instance

## How to Test
- Unit tests are included in the test/ directory.
- Run tests manually with:
```bash
pytest --junitxml=results.xml
```
- The Jenkins pipeline automatically runs these tests and generates reports.

## Known Issues
- Ensure that the webhook URL is accessible from GitHub for automated builds.
- If using Podman, adapt the Docker commands accordingly.

## Jenkins Agent with Docker

The jenkins-agent directory contains a Dockerfile that can be used if you wish to run Jenkins agent in a Docker container. 

This is not mandatory for this project.

To use Docker as the Jenkins agent:

#### 1. Navigate to the Dockerfile Location:

```bash
cd jenkins-agent
```

#### 2. Build and Push the Image: Build and tag the image, then push it to Docker Hub:
```bash
docker build -t <your-dockerhub-username>/jenkins-agent:latest .
docker push <your-dockerhub-username>/jenkins-agent:latest
```

#### 3. Update build.Jenkinsfile:
Replace 'agent any' with:

```bash
agent {
    docker {
        image '<your-dockerhub-username>/jenkins-agent:latest'
        args '--user root -v /var/run/docker.sock:/var/run/docker.sock'
    }
}
```

## Conclusion
This project demonstrates containerization, deployment, and CI/CD automation using tools like Docker, Jenkins, and Nginx. 

The setup is scalable and can be easily adapted for different applications and environments.