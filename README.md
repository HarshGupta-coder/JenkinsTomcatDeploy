# JenkinsTomcatDeploy
 Automates deployment to Tomcat using Jenkins. Streamline your CI/CD pipeline with this efficient integration



```markdown
# Deploy to Tomcat Server

This guide outlines the steps to deploy a project to a Tomcat server using Jenkins automation.

## Step 1: Setup Jenkins Server

### Launch EC2 Instance
- Launch an EC2 instance with Amazon Linux 2 AMI.
- Open TCP ports 8080 (Jenkins UI) and 22 (SSH) in the Security Group.

### Install Jenkins and Java
- Install Java:
  ```
  sudo amazon-linux-extras install java-openjdk11
  ```
- Install Jenkins:
  ```
  sudo yum install jenkins
  ```

### Start Jenkins Service
- Start Jenkins service:
  ```
  sudo systemctl start jenkins
  sudo systemctl enable jenkins
  ```

### Optional: Set Hostname
- Set a hostname for the server (Optional):
  ```
  sudo vi /etc/hostname
  ```
  Enter `jenkins-server` in the file.

## Step 2: Integrate Git with Jenkins

### Install Git
- Install Git on Jenkins server:
  ```
  sudo yum install git
  ```

### Install GitHub Plugin
- Install GitHub plugin on Jenkins UI.

## Step 3: Integrate Maven with Jenkins

### Setup Maven
- Navigate to the `/opt` directory:
  ```
  cd /opt
  ```
- Download and extract Apache Maven.
- Set up environment variables for Java and Maven.

### Configure Maven in Jenkins
- Install Maven Integration plugin on Jenkins UI.
- Configure Maven in Jenkins settings.

## Step 4: Setup Tomcat Server

### Launch EC2 Instance
- Launch another EC2 instance with the same Security Group and AMI.

### Install Java and Tomcat
- Install Java:
  ```
  sudo amazon-linux-extras install java-openjdk11
  ```
- Download and extract Apache Tomcat.

### Create Symbolic Links
- Create symbolic links for starting and stopping Tomcat.

### Update Configuration Files
- Update `context.xml` file to access the Manager page.
- Update `tomcat-users.xml` file for Manager access.

### Restart Tomcat Service
- Restart Tomcat service.

## Step 5: Integrate Tomcat with Jenkins

### Install Jenkins Plugin
- Install "Deploy to container" plugin on Jenkins UI.

### Add Tomcat Credentials
- Add Tomcat credentials in Jenkins settings.

## Step 6: Configure Jenkins Job

### Create Jenkins Job
- Create a new Jenkins job (e.g., "deployToTomcat") as a Maven project.

### Configure SCM
- Configure Git repository URL in SCM section.

### Set Maven Build Goals
- Set Maven build goals and options to `clean install`.

### Add Post-Build Action
- Add a post-build action "Deploy war/ear to a container".

```
