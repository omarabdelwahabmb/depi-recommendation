Depi-Recommendation 
A simple distributed application running with an Automated Deployment Pipeline using Jenkins, Docker, Docker Compose, Ansible, and AWS.

Technologies Used
Docker: Containerizes the backend and frontend applications for isolated and consistent environments.
Jenkins: Manages the CI/CD pipeline to automate builds, tests, and deployments.
Ansible: Automates server setup and deployment tasks.
Docker Compose: Orchestrates multi-container Docker applications, simplifying the deployment process without Kubernetes.
AWS: Provides cloud infrastructure for hosting the application.


Architecture
The updated deployment pipeline includes:
Jenkins: Automated build, test, and deploy process.
Docker: Containerization of the backend and frontend applications.
Docker Compose: Manages the orchestration of containers, ensuring multi-container applications run smoothly.
Ansible: Automates server setup, Docker installation, and deployment of the app using Docker Compose on AWS.
AWS (EC2 instances): Hosts the Docker containers using Docker Compose for orchestration.

![napkin-selection (3)](https://github.com/user-attachments/assets/bfd1ccf0-025f-4bf4-b3a9-55538369e762)

Getting Started
Prerequisites
To set up and run this project, you will need the following:

Docker and Docker Compose installed on your local machine.
Jenkins installed and running (either locally or on a server).
Ansible installed on the Jenkins server.
AWS account with EC2 .

Deployment Process
Jenkins Pipeline Setup
1-The pipeline is managed via Jenkins and includes the following stages:

2-Checkout code: Fetches the latest code from the Git repository.

3-Build Docker images: Builds Docker images for both the backend and frontend applications.

4-Run Tests: Runs unit tests to ensure code quality.

5-Push Docker images: Pushes the built images to Docker Hub.

6-Deploy with Ansible: Ansible provisions EC2 instances, sets up Docker, and deploys the application using Docker Compose.


Security Measures
The following security practices are applied:

Secrets Management: Sensitive information like database credentials are stored securely using Jenkins and HashiCorp Vault.
SSH Keys: Only SSH key-based authentication is allowed for server access.

Contributing
We welcome contributions to improve this project. Please follow these steps:

Fork the repository.
Create a feature branch.
Commit your changes.
Push the branch to your fork.
Open a pull request.

