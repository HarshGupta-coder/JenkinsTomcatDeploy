# Deploy to Tomcat Server

This guide outlines the steps to deploy a project to a Tomcat server using Jenkins automation.

![Screenshot (120)](https://github.com/HarshGupta-coder/JenkinsTomcatDeploy/assets/54001485/93bb7873-8eaa-43e8-9c43-32a4ce66c574)


## Step 1: Setup Jenkins Server

- Launch an EC2 instance with Amazon Linux 2 AMI.
- Open TCP ports 8080 (Jenkins UI) and 22 (SSH) in the Security Group.
- Install Java:
  ```bash
  sudo amazon-linux-extras install java-openjdk11
  ```
- Install Jenkins:
  ```bash
  sudo yum install jenkins
  ```
- Start and enable Jenkins service:
  ```bash
  sudo systemctl start jenkins
  sudo systemctl enable jenkins
  ```
- (Optional) Set a hostname for the server:
  ```bash
  sudo vi /etc/hostname
  ```
  Enter `jenkins-server` in the file.

## Step 2: Integrate Git with Jenkins

- Install Git on Jenkins server:
  ```bash
  sudo yum install git
  ```
- Install GitHub plugin on Jenkins UI.

## Step 3: Integrate Maven with Jenkins

- Navigate to the `/opt` directory:
  ```bash
  cd /opt
  ```
- Download and extract Apache Maven:
  ```bash
  wget <Maven tar file link>
  tar -xvzf <Maven tar file>
  mv <extracted directory> maven
  ```
- Set up environment variables for Java and Maven:
  ```bash
  vi ~/.bash_profile
  ```
  Add the following lines:
  ```bash
  M2=/opt/maven/bin
  M2_HOME=/opt/maven
  JAVA_HOME=<path to Java>
  PATH=$PATH:$HOME/bin:$JAVA_HOME:$M2:$M2_HOME
  ```
  - Use the following command to find the path to Java:
    ```bash
    find /usr/lib/jvm -name java
    ```
- Apply changes:
  ```bash
  source ~/.bash_profile
  ```
- Verify Maven installation:
  ```bash
  mvn -v
  ```
- Install Maven Integration plugin on Jenkins UI.
- Configure Maven in Jenkins:
  - Manage Jenkins > Global Tool Configuration
  - Add Maven: Name - `maven`, MAVEN_HOME - `/opt/maven`, untick "Install automatically"
  - Add Java: Name - `java`, JAVA_HOME - `<path to Java>`, untick "Install automatically"
  - Use the following command to find the path to Java:
    ```bash
    find /usr/lib/jvm -name java
    ```

## Step 4: Setup Tomcat Server

- Launch another EC2 instance with the same Security Group and AMI.
- Download and extract Apache Tomcat.
- Install Java:
  ```bash
  sudo amazon-linux-extras install java-openjdk11
  ```
- Create symbolic links for starting and stopping Tomcat:
  ```bash
  ln -s /opt/tomcat/bin/startup.sh /usr/local/bin/tomcatup
  ln -s /opt/tomcat/bin/shutdown.sh /usr/local/bin/tomcatdown
  ```
- Update `context.xml` file to access the Manager page:
  ```bash
  find /opt/tomcat -name context.xml
  ```
  Edit the `context.xml` files in the `webapps/host-manager/META-INF` and `webapps/manager/META-INF` directories. Comment out the following lines in these files:
  ```xml
  <!--
  <Valve className= .......
          ...........::1|0:0:0:1" />
  -->
  ```
- Update `tomcat-users.xml` file for Manager access:
  ```bash
  sudo vi /opt/tomcat/conf/tomcat-users.xml
  ```
  Add the following lines to the end of the file:
  ```xml
  <role rolename="manager-gui"/>
  <role rolename="manager-script"/>
  <role rolename="manager-jmx"/>
  <role rolename="manager-status"/>
  <user username="admin" password="admin" roles="manager-gui, manager-script, manager-jmx, manager-status"/>
  <user username="deployer" password="deployer" roles="manager-script"/>
  <user username="tomcat" password="s3cret" roles="manager-gui"/>
  ```
- Restart Tomcat service:
  ```bash
  tomcatdown
  tomcatup
  ```

## Step 5: Integrate Tomcat with Jenkins

- Install "Deploy to container" plugin on Jenkins UI.
- Add Tomcat credentials in Jenkins:
  - Manage Jenkins > Credentials > Global
  - Add new credentials of type Username and Password.

## Step 6: Configure the Jenkins Job

- Create a new Jenkins job (e.g., "deployToTomcat") as a Maven project.
- Configure Git repository URL in SCM section.
- Set Maven build goals and options to `clean install`.
- Add a post-build action "Deploy war/ear to a container":
  - WAR/EAR files: `**/*.war`
  - Add container: `Tomcat 8.x Remote`
  - Credentials: Use previously added credentials
  - Tomcat URL: `http://<instance_ip>:8080/`

Speacial Thanks to AR Shankar | Valaxy Technologies (AWS, DevOps Specialist)
