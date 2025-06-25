# Jenkins Training Materials

<div align="center">
  <img src="./images/jenkins.jpg" alt="Jenkins Logo" width="400" />
</div>

## What is Jenkins?

Jenkins is an **open-source automation server** that helps automate the parts of software development related to building, testing, and deploying, facilitating continuous integration and continuous delivery (CI/CD).

### Key Features
- **Continuous Integration/Continuous Delivery** - Automate your build, test, and deployment pipeline
- **Extensible** - Over 1,800 plugins to support building, deploying, and automating any project
- **Distributed** - Easily distribute work across multiple machines for faster builds
- **Easy Configuration** - Web-based GUI and can be configured via code (Configuration as Code)
- **Open Source** - Free to use with a large, active community

### Why Use Jenkins?
1. **Automation** - Reduces manual effort and human errors
2. **Fast Feedback** - Quickly know if your code changes broke something
3. **Consistency** - Same build process every time
4. **Integration** - Works with virtually any tool in your development stack
5. **Visibility** - Clear view of your build/deployment pipeline status

## Training Topics

### 📚 Available Documentation

1. **[Job Types](./Job-Types.md)**
   - Learn about different Jenkins job types (Freestyle, Pipeline, Multibranch, etc.)
   - Understand when to use each job type
   - See examples and best practices

2. **[Cron Load Spreading](./Cron-Load-Spread.md)**
   - Understand Jenkins' special `H` (hash) syntax for cron scheduling
   - Learn how to prevent the "thundering herd" problem
   - Best practices for scheduling periodic jobs

3. **[Background Processes](./Background-Process.md)**
   - How to start processes that survive after Jenkins build completion
   - Using `BUILD_ID=dontKillMe` for background processes
   - Managing long-running services started by Jenkins

4. **[Gmail SMTP Configuration](./Gmail-SMTP-Config.md)**
   - Step-by-step guide to configure Gmail SMTP in Jenkins
   - Creating Google App Passwords for authentication
   - Troubleshooting common email notification issues

## Getting Started with Jenkins

### Installation
Jenkins can be installed on various platforms:
- **Docker**: `docker run -p 8080:8080 jenkins/jenkins:lts`
- **WAR file**: Download and run with `java -jar jenkins.war`
- **Native packages**: Available for Windows, macOS, and Linux

### First Steps
1. Access Jenkins at `http://<ip-address>:8080`
2. Complete the initial setup wizard
3. Install suggested plugins
4. Create your first admin user
5. Start creating jobs!

### Basic Concepts
- **Job/Project**: A task or set of tasks that Jenkins executes
- **Build**: A single execution of a job
- **Workspace**: Directory where Jenkins runs your builds
- **Node/Agent**: A machine that can run Jenkins jobs
- **Plugin**: Extensions that add functionality to Jenkins

## Best Practices
- Use Pipeline jobs for complex workflows
- Store Jenkinsfiles in version control
- Use folders to organize related jobs
- Implement proper security and access controls
- Regularly backup Jenkins configuration
- Monitor Jenkins performance and disk usage

## Additional Resources
- [Official Jenkins Documentation](https://www.jenkins.io/doc/)
- [Jenkins Pipeline Syntax](https://www.jenkins.io/doc/book/pipeline/syntax/)
- [Jenkins Plugins](https://plugins.jenkins.io/)
- [Jenkins Community](https://www.jenkins.io/participate/)