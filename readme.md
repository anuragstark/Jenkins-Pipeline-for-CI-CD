# Jenkins CI/CD Pipeline for Web Application

A complete CI/CD pipeline implementation using Jenkins and Docker to automate the build, test, and deployment process of a web application.

## Project Overview

This project demonstrates a fully functional CI/CD pipeline that automatically builds, tests, and deploys a web application using modern DevOps practices. The pipeline is triggered on every code commit and ensures consistent, reliable deployments.

## Technologies Used

- **Jenkins** - CI/CD automation server
- **Docker** - Containerization platform
- **Git** - Version control system
- **Nginx** - Web server (via Docker container)
- **HTML/CSS-Tailwind/JavaScript** - Frontend technologies
- **CentOS 9** - Operating system

## Features

- **Automated CI/CD Pipeline** with 5 distinct stages
- **Docker containerization** for consistent deployments
- **Automated testing** to ensure application reliability
- **Port conflict resolution** and container management

## Pipeline Stages

The Jenkins pipeline consists of the following stages:

1. **Checkout** - Retrieves the latest code from GitHub repository
2. **Build** - Creates Docker image from the application code
3. **Test** - Runs automated tests to verify application functionality
4. **Deploy** - Deploys the containerized application
5. **Verify Deployment** - Confirms successful deployment

## Project Structure

```
Jenkins-Pipeline-for-CI-CD/
├── Jenkinsfile                 # Jenkins pipeline configuration
├── Dockerfile                  # Docker container configuration
├── Images/                     # Output images and job snaps 
├── index.html                  # Main web application file
└──  README.md                   # Project documentation
```

## Prerequisites

Before setting up this pipeline, ensure you have:

- **Jenkins** installed and running
- **Docker** installed and configured
- **Git** installed
- **GitHub account** with repository access
- **CentOS 9** or compatible Linux distribution

### Quick Setup (Recommended)

Run this script to install all required tools automatically:

```
git clone https://github.com/anuragstark/Prereqs-for-DevOps.git
cd Prereqs-for-DevOps/centos
bash install.sh
```

## Setup Instructions

### 1. Clone the Repository

```bash
git clone https://github.com/anuragstark/Jenkins-Pipeline-for-CI-CD.git
cd Jenkins-Pipeline-for-CI-CD
```

### 2. Jenkins Configuration

1. Open Jenkins dashboard (`http://localhost:8080`)
2. Create a new Pipeline job:
   - Click "New Item"
   - Enter job name: `web-app-pipeline`
   - Select "Pipeline" and click "OK"

3. Configure the pipeline:
   - **Definition**: Pipeline script from SCM
   - **SCM**: Git
   - **Repository URL**: `https://github.com/anuragstark/Jenkins-Pipeline-for-CI-CD.git`
   - **Branch Specifier**: `*/main`
   - **Script Path**: `Jenkinsfile`

### 3. Docker Permissions

Ensure Jenkins can access Docker:

```bash
# Add jenkins user to docker group
sudo usermod -aG docker jenkins

# Restart Jenkins service
sudo systemctl restart jenkins

# Verify Docker access
sudo -u jenkins docker ps
```

### 4. Build and Deploy

1. Click "Build Now" in Jenkins dashboard
2. Monitor the pipeline execution through console output
3. Access the deployed application at `http://localhost:8090`

## Configuration Details

### Environment Variables

The pipeline uses the following environment variables:

- `DOCKER_IMAGE`: Name of the Docker image (`my-web-app`)
- `DOCKER_TAG`: Build number for image versioning
- `CONTAINER_NAME`: Name of the deployed container (`web-app-container`)
- `PORT`: Application port (`8090`)

### Docker Configuration

The application runs in an Nginx Alpine container:

```dockerfile
FROM nginx:alpine
COPY *.html /usr/share/nginx/html/
COPY . /usr/share/nginx/html/
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

## Testing

The pipeline includes automated testing that:

- Verifies Docker image builds successfully
- Tests container startup and health
- Validates application accessibility
- Performs cleanup operations

## Deployment

The application is deployed as a Docker container with:

- **Port mapping**: Host port 8090 → Container port 80
- **Restart policy**: `unless-stopped`
- **Health checks**: Automatic verification post-deployment

## Monitoring and Logs

### Check Application Status

```bash
# View running containers
docker ps | grep web-app-container

# Check application logs
docker logs web-app-container

# Test application endpoint
curl http://localhost:8090
```

### Jenkins Pipeline Monitoring

- Access Jenkins dashboard for build history
- View console output for detailed execution logs
- Monitor pipeline stages and execution times
- Set up notifications for build status

## Continuous Integration Workflow

1. **Developer pushes code** to GitHub repository
2. **Jenkins detects changes** (via webhook or polling)
3. **Pipeline automatically triggers** and executes all stages
4. **Application is built, tested, and deployed** without manual intervention
5. **Notifications sent** on success or failure

## Error Handling

The pipeline includes robust error handling:

- **Port conflict resolution**: Uses alternative ports if conflicts occur
- **Container cleanup**: Removes failed deployments automatically  
- **Rollback capabilities**: Maintains previous working version
- **Detailed logging**: Comprehensive error reporting

## Troubleshooting

### Common Issues and Solutions

**Port 8090 already in use:**
```bash
# Find process using the port
sudo netstat -tlnp | grep :8090
# Kill the process or use different port
```

**Docker permission denied:**
```bash
# Add user to docker group
sudo usermod -aG docker $USER
# Restart session or reboot
```

**Jenkins can't access Docker:**
```bash
# Restart Jenkins service
sudo systemctl restart jenkins
```

## 👤 Author

**Anurag Chauhan**
- GitHub: [@anuragstark](https://github.com/anuragstark)
- LinkedIn: [Connect with me](https://linkedin.com/in/anuragstark)

## 🙏 Acknowledgments

- Jenkins community for excellent documentation
- Docker team for containerization technology
- Open source community for tools and resources

## 📞 Support

If you encounter any issues or have questions:

1. Check the [Issues](https://github.com/anuragstark/Jenkins-Pipeline-for-CI-CD/issues) section
2. Create a new issue with detailed description
3. Contact the maintainer through GitHub

---

<!-- ⭐ **Star this repository if you found it helpful!** ⭐ -->
