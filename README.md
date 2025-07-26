# Jenkins Complete Learning Guide - Zero to Hero

## Table of Contents
1. [Day 1: Jenkins Fundamentals](#day-1-jenkins-fundamentals)
2. [Day 2-3: Variables and Parameters](#day-2-3-variables-and-parameters)
3. [Day 4: Pipeline Fundamentals](#day-4-pipeline-fundamentals)
4. [Day 5: Master-Slave Architecture](#day-5-master-slave-architecture)
5. [Day 6: Code Quality with SonarQube](#day-6-code-quality-with-sonarqube)
6. [Day 7: Deployment with Tomcat](#day-7-deployment-with-tomcat)

---

## Day 1: Jenkins Fundamentals

### What is Jenkins?

**Jenkins** is a free, open-source automation server that enables Continuous Integration (CI) and Continuous Deployment (CD).

#### Key Concepts:
- **CI (Continuous Integration)**: Continuous Build + Continuous Test (Old Code + New Code)
- **CD (Continuous Delivery)**: Deployment to Production is Manual
- **CD (Continuous Deployment)**: Deployment to Production is Automatic

#### Environment Flow:
```
DEV (Developers) → TEST/QA (Testers) → UAT (Client) → PROD (Users)
```

#### Pipeline Flow:
```
CODE → BUILD → TEST → WAR → DEV/TEST/UAT/PROD
```

### Jenkins Features:
- Free and Open Source
- Written in Java-11
- Platform Independent
- Plugin-based architecture
- Community Support
- Can automate entire SDLC
- Default Port: 8080

### 🛠️ Hands-on Exercise 1: Jenkins Installation

**Prerequisites**: AWS EC2 instance with Amazon Linux

```bash
# Step 1: Install dependencies
yum install git java-1.8.0-openjdk maven -y

# Step 2: Add Jenkins repository
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key

# Step 3: Install Java 11 and Jenkins
amazon-linux-extras install java-openjdk11 -y
yum install jenkins -y
update-alternatives --config java

# Step 4: Start Jenkins service
systemctl start jenkins.service
systemctl status jenkins.service
```

**Access Jenkins**: `http://your-public-ip:8080`

**Get Initial Password**:
```bash
cat /var/lib/jenkins/secrets/initialAdminPassword
```

### 🛠️ Hands-on Exercise 2: Creating Your First Job

1. **New Item** → Name: "my-first-job" → **Freestyle Project** → **OK**
2. **Source Code Management** → **Git** → Repository URL: `https://github.com/devopsbyraham/jenkins-java-project.git`
3. **Build Steps** → **Add Build Step** → **Execute Shell**
4. Command: `mvn clean package`
5. **Save** → **Build Now**

**What happens**: Jenkins will clone the repository, compile the Java code, run tests, and create a WAR file.

---

## Day 2-3: Variables and Parameters

### Types of Variables in Jenkins

#### 1. Local Variables
Work only inside the specific job.

**Example**:
```bash
name=john
echo "Hello, my name is $name. $name is learning DevOps."
```

#### 2. Global Variables
Work across all jobs in Jenkins.

**Setup**: Dashboard → Manage Jenkins → System → Global Properties → Environment Variables

**Example**:
- Name: `COMPANY_NAME`
- Value: `TechCorp`

**Usage in job**:
```bash
echo "Welcome to $COMPANY_NAME"
```

#### 3. Jenkins Environment Variables
Built-in variables that change with each build.

**Common Examples**:
```bash
echo "Build Number: $BUILD_NUMBER"
echo "Job Name: $JOB_NAME"
echo "Build URL: $BUILD_URL"
echo "Workspace: $WORKSPACE"
```

### 🛠️ Hands-on Exercise 3: Working with Variables

Create a new job and add this shell script:
```bash
# Local variable
department=DevOps
echo "Department: $department"

# Global variable (assuming you set COMPANY_NAME)
echo "Company: $COMPANY_NAME"

# Jenkins environment variables
echo "This is build #$BUILD_NUMBER of job $JOB_NAME"
echo "Build started at: $BUILD_TIMESTAMP"
echo "Jenkins URL: $JENKINS_URL"
```

### Parameters in Jenkins

Parameters allow you to pass inputs to jobs at runtime.

#### 1. Choice Parameter
**Setup**: Configure Job → This project is parameterized → Choice Parameter
- Name: `ENVIRONMENT`
- Choices: `dev`, `test`, `uat`, `prod`

**Usage**:
```bash
echo "Deploying to $ENVIRONMENT environment"
```

#### 2. String Parameter
- Name: `VERSION`
- Default Value: `1.0.0`

#### 3. Boolean Parameter
- Name: `SKIP_TESTS`
- Default: `false`

### 🛠️ Hands-on Exercise 4: Parameterized Job

1. Create job → Configure → **This project is parameterized**
2. Add Choice Parameter: `ENVIRONMENT` (dev, test, prod)
3. Add String Parameter: `APP_VERSION` (default: 1.0.0)
4. Add Boolean Parameter: `SKIP_TESTS`
5. Build Step:
```bash
echo "Deploying version $APP_VERSION to $ENVIRONMENT"
if [ "$SKIP_TESTS" = "true" ]; then
    echo "Skipping tests as requested"
else
    echo "Running tests..."
    mvn test
fi
```

### Build Triggers

#### 1. Build Periodically (Cron Jobs)
Schedule builds using cron syntax: `* * * * *` (minute hour date month day-of-week)

**Examples**:
- `0 9 * * *` - Every day at 9 AM
- `0 */2 * * *` - Every 2 hours
- `*/15 * * * *` - Every 15 minutes

#### 2. Poll SCM
Check for code changes at regular intervals.

#### 3. GitHub Webhook
Trigger build immediately when code is pushed.

**Setup**:
1. GitHub repo → Settings → Webhooks → Add webhook
2. Payload URL: `http://your-jenkins-url:8080/github-webhook/`
3. Content type: `application/json`
4. Job configuration → Build Triggers → GitHub hook trigger for GITScm polling

### 🛠️ Hands-on Exercise 5: Automated Builds

1. Create a job with Poll SCM: `*/2 * * * *` (every 2 minutes)
2. Make a change to your GitHub repository
3. Watch Jenkins automatically detect and build the change

---

## Day 4: Pipeline Fundamentals

### What is a Pipeline?
A pipeline is a step-by-step execution of a process - a series of interconnected events.

### Types of Pipelines:
1. **Declarative** (Recommended)
2. **Scripted** (Legacy)

### Pipeline Syntax (Remember: PASSS)
- **P**ipeline
- **A**gent
- **S**tages
- **S**tage
- **S**teps

### 🛠️ Hands-on Exercise 6: Your First Pipeline

**Single Stage Pipeline**:
```groovy
pipeline {
    agent any 
    
    stages {
        stage('Hello World') {
            steps {
                echo "Hello World from Jenkins Pipeline!"
            }
        }
    }
}
```

**Multi-Stage Pipeline**:
```groovy
pipeline {
    agent any 
    
    stages {
        stage('Preparation') {
            steps {
                echo "Preparing the environment..."
            }
        }
        stage('Build') {
            steps {
                echo "Building the application..."
                sh 'touch build-artifact.jar'
            }
        }
        stage('Test') {
            steps {
                echo "Running tests..."
                sh 'echo "All tests passed!" > test-results.txt'
            }
        }
    }
}
```

### 🛠️ Hands-on Exercise 7: CI Pipeline

**Complete CI Pipeline**:
```groovy
pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/devopsbyraham/jenkins-java-project.git'
            }
        }
        stage('Compile') {
            steps {
                sh 'mvn compile'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Package') {
            steps {
                sh 'mvn package'
            }
        }
    }
}
```

### Pipeline as Code (Multiple Actions in Single Stage)

```groovy
pipeline {
    agent any
    
    stages {
        stage('CI Process') {
            steps {
                git 'https://github.com/devopsbyraham/jenkins-java-project.git'
                sh '''
                    mvn compile
                    mvn test
                    mvn package
                '''
            }
        }
    }
}
```

### Input Parameters in Pipeline

```groovy
pipeline {
    agent any
    
    stages {
        stage('Build') {
            steps {
                sh 'mvn compile'
            }
        }
        stage('Deploy') {
            input {
                message "Deploy to production?"
                ok "Yes, Deploy!"
            }
            steps {
                echo "Deploying to production..."
                sh 'mvn package'
            }
        }
    }
}
```

### Post-Build Actions

```groovy
pipeline {
    agent any
    
    stages {
        stage('Build') {
            steps {
                sh 'mvn compile'
            }
        }
    }
    
    post {
        always {
            echo "This runs regardless of build result"
        }
        success {
            echo "Build succeeded!"
        }
        failure {
            echo "Build failed!"
        }
    }
}
```

### 🛠️ Hands-on Exercise 8: Advanced Pipeline

Create this pipeline and experiment with different scenarios:

```groovy
pipeline {
    agent any
    
    parameters {
        choice(name: 'ENVIRONMENT', choices: ['dev', 'test', 'prod'], description: 'Target environment')
        booleanParam(name: 'SKIP_TESTS', defaultValue: false, description: 'Skip test execution')
    }
    
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/devopsbyraham/jenkins-java-project.git'
            }
        }
        
        stage('Build') {
            steps {
                sh 'mvn compile'
            }
        }
        
        stage('Test') {
            when {
                not { params.SKIP_TESTS }
            }
            steps {
                sh 'mvn test'
            }
        }
        
        stage('Package') {
            steps {
                sh 'mvn package'
            }
        }
        
        stage('Deploy') {
            steps {
                echo "Deploying to ${params.ENVIRONMENT} environment"
            }
        }
    }
    
    post {
        success {
            echo "Pipeline completed successfully!"
        }
        failure {
            echo "Pipeline failed. Check the logs."
        }
    }
}
```

---

## Day 5: Master-Slave Architecture

### Why Master-Slave?
- Distribute build load
- Reduce load on Jenkins master
- Support different platforms
- Parallel execution

### 🛠️ Hands-on Exercise 9: Setting up Jenkins Slave

**Step 1: Prepare Slave Server**
```bash
# On slave server
amazon-linux-extras install java-openjdk11 -y
yum install git java-1.8.0-openjdk maven -y
```

**Step 2: Configure Slave in Jenkins**
1. Dashboard → Manage Jenkins → Nodes and Clouds → New Node
2. Node name: `build-slave-1`
3. Type: Permanent Agent
4. Configuration:
   - Number of executors: `3`
   - Remote root directory: `/tmp`
   - Labels: `build-server`
   - Launch method: Launch agents via SSH
   - Host: `slave-private-ip`
   - Credentials: Add SSH key
   - Host Key Verification Strategy: Non verifying

**Step 3: Pipeline with Slave**
```groovy
pipeline {
    agent {
        label 'build-server'
    }
    
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/devopsbyraham/jenkins-java-project.git'
            }
        }
        stage('Build on Slave') {
            steps {
                sh 'mvn compile'
            }
        }
        stage('Test on Slave') {
            steps {
                sh 'mvn test'
            }
        }
    }
}
```

### Custom Workspace

```bash
# Create custom directory
mkdir /opt/jenkins-workspace
chown jenkins:jenkins /opt/jenkins-workspace
```

Job → Configure → Advanced → Use custom workspace: `/opt/jenkins-workspace`

---

## Day 6: Code Quality with SonarQube

### What is SonarQube?
- Code quality analysis tool
- Supports 20+ programming languages
- Identifies bugs, vulnerabilities, code smells

### 🛠️ Hands-on Exercise 10: SonarQube Setup

**Installation Script**:
```bash
#!/bin/bash
# Launch t2.medium instance with port 9000 open
cd /opt/
wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-8.9.6.50800.zip
unzip sonarqube-8.9.6.50800.zip
amazon-linux-extras install java-openjdk11 -y
useradd sonar
chown sonar:sonar sonarqube-8.9.6.50800 -R
chmod 777 sonarqube-8.9.6.50800 -R
```

**Start SonarQube**:
```bash
su - sonar
cd /opt/sonarqube-8.9.6.50800/bin/linux/
sh sonar.sh start
sh sonar.sh status
```

**Access**: `http://your-ip:9000` (admin/admin)

### 🛠️ Hands-on Exercise 11: Pipeline with Code Quality

```groovy
pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/devopsbyraham/jenkins-java-project.git'
            }
        }
        
        stage('Build') {
            steps {
                sh 'mvn compile'
            }
        }
        
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        
        stage('Code Quality Analysis') {
            steps {
                sh '''
                    mvn sonar:sonar \
                      -Dsonar.projectKey=my-project \
                      -Dsonar.host.url=http://your-sonar-ip:9000 \
                      -Dsonar.login=your-sonar-token
                '''
            }
        }
        
        stage('Package') {
            steps {
                sh 'mvn clean package'
            }
        }
    }
}
```

---

## Day 7: Deployment with Tomcat

### What is Tomcat?
- Application server for Java applications
- Deploys WAR and JAR files
- Free and platform-independent

### 🛠️ Hands-on Exercise 12: Tomcat Setup

**Installation**:
```bash
# Download and extract Tomcat
wget https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.80/bin/apache-tomcat-9.0.80.tar.gz
tar -zxvf apache-tomcat-9.0.80.tar.gz
cd apache-tomcat-9.0.80

# Configure manager access
vim webapps/manager/META-INF/context.xml
# Delete/comment lines 21 and 22

# Add user
vim conf/tomcat-users.xml
# Add before </tomcat-users>:
```

```xml
<role rolename="manager-gui"/>
<role rolename="manager-script"/>
<user username="tomcat" password="admin123" roles="manager-gui, manager-script"/>
```

**Start Tomcat**:
```bash
./bin/startup.sh
```

**Access**: `http://your-ip:8080`

### 🛠️ Hands-on Exercise 13: Complete CI/CD Pipeline

**Install Plugin**: "Deploy to container"

**Add Credentials**: Manage Jenkins → Credentials → Add → Username/Password (tomcat/admin123)

**Complete Pipeline**:
```groovy
pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/devopsbyraham/jenkins-java-project.git'
            }
        }
        
        stage('Build') {
            steps {
                sh 'mvn compile'
            }
        }
        
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        
        stage('Code Quality') {
            steps {
                sh '''
                    mvn sonar:sonar \
                      -Dsonar.projectKey=netflix \
                      -Dsonar.host.url=http://your-sonar-ip:9000 \
                      -Dsonar.login=your-token
                '''
            }
        }
        
        stage('Package') {
            steps {
                sh 'mvn clean package'
            }
        }
        
        stage('Deploy to Tomcat') {
            steps {
                deploy adapters: [
                    tomcat9(
                        credentialsId: 'tomcat-credentials',
                        path: '',
                        url: 'http://your-tomcat-ip:8080/'
                    )
                ],
                contextPath: 'myapp',
                war: 'target/*.war'
            }
        }
    }
    
    post {
        success {
            echo "Application deployed successfully!"
        }
        failure {
            echo "Deployment failed!"
        }
    }
}
```

## Advanced Topics

### RBAC (Role-Based Access Control)

**Setup Users**:
1. Manage Jenkins → Users → Create User

**Install Plugin**: "Role-based Authorization Strategy"

**Configure Roles**:
1. Manage Jenkins → Security → Authorization → Role-Based Strategy
2. Manage Roles → Add roles (fresher, experienced)
3. Assign Roles → Map users to roles

### Jenkins Backup

**Install Plugin**: "Periodic Backup"

**Configure**: Manage Jenkins → Periodic Backup Manager

### Troubleshooting Common Issues

1. **Build Fails**: Check console output, verify dependencies
2. **SonarQube Connection**: Verify URL and token
3. **Deployment Issues**: Check Tomcat logs, verify credentials
4. **Slave Connection**: Verify SSH connectivity, Java installation

### Best Practices

1. **Use Pipelines**: More maintainable than freestyle jobs
2. **Version Control**: Store Jenkinsfiles in repositories
3. **Resource Management**: Use appropriate agent labels
4. **Security**: Regular credential rotation, RBAC implementation
5. **Monitoring**: Regular backup, log monitoring
6. **Clean Workspace**: Regular cleanup of old builds

## Practice Projects

### Project 1: Basic CI Pipeline
- Fork a Java project from GitHub
- Create pipeline: checkout → compile → test → package
- Add email notifications

### Project 2: Multi-Environment Deployment
- Create pipeline with dev/test/prod stages
- Use parameters for environment selection
- Add approval gates for production

### Project 3: Complete DevOps Pipeline
- Integrate SonarQube for code quality
- Deploy to Tomcat server
- Add post-deployment testing
- Implement rollback mechanism

## Next Steps

1. **Learn Docker Integration**: Containerized builds and deployments
2. **Kubernetes Deployment**: Deploy to K8s clusters
3. **Advanced Plugins**: Explore plugin ecosystem
4. **Infrastructure as Code**: Terraform integration
5. **Monitoring**: Integrate with monitoring tools
6. **Security**: Advanced security practices

---

**Congratulations!** You've completed the Jenkins zero-to-hero journey. Practice these exercises, experiment with different configurations, and gradually build more complex pipelines. The key to mastering Jenkins is hands-on practice and continuous learning.
