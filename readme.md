# Study Jenkins 🔧

This repository contains comprehensive guides for Jenkins installation, configuration, and advanced pipeline implementation.

## Installation 🔧

- **Prerequisites**

  Before installing Jenkins, ensure you have:
  - **Java 17** or higher installed
  - **Git** for version control
  - **Sufficient server resources** (minimum 256 MB RAM, 1 GB storage)
  - **SSH access** to your server/VPS

- **Installation**

  `Download Jenkins:`
  ```bash
  wget https://get.jenkins.io/war-stable/2.528.1/jenkins.war
  ```
  
  `Start Jenkins:`
  ```bash
  java -jar jenkins.war --httpPort=8080 &
  ```
  
  `Access Jenkins:`
  ```
  http://YOUR_SERVER_IP:8080
  ```
  
  `Unlock Jenkins:`
  ```bash
  cat /root/.jenkins/secrets/initialAdminPassword
  ```

## List of Material 📚

- 📘 **Jenkins Installation & Setup**

  Complete guide for setting up Jenkins on a VPS, from initial installation to advanced configuration:
  
  ```bash
  # Install prerequisites
  sudo apt-get install git
  sudo apt install openjdk-17-jdk -y
  
  # Start Jenkins
  java -jar jenkins.war --httpPort=8080 &
  
  # Process management
  ps aux | grep jenkins
  kill -9 <PID>
  pkill -f jenkins.war
  ```
  
  **Topics covered:**
  - System requirements and VPS setup
  - Prerequisites installation (Git, Java)
  - Jenkins installation and startup
  - Initial configuration and plugin management
  - Job management (create, build, delete)
  - Git integration and SSH key setup
  - Build configuration and post-build actions
  - Slack notifications integration
  - Advanced job management (copy, disable/enable)
  - Triggers and scheduling (cron, poll SCM)
  - Environment variables and build parameters
  - Build history management and executors
  - User management and role-based security
  - Custom views and distributed builds
  - Monitoring and troubleshooting

- 📗 **Jenkins Pipeline**

  Comprehensive guide for implementing Continuous Delivery pipelines using Groovy DSL:
  
  ```groovy
  // Basic pipeline structure
  pipeline {
    agent any
    stages {
      stage("Build") {
        steps {
          echo "Building..."
        }
      }
      stage("Test") {
        steps {
          echo "Testing..."
        }
      }
      stage("Deploy") {
        steps {
          echo "Deploying..."
        }
      }
    }
    post {
      success {
        echo "Pipeline succeeded!"
      }
      failure {
        echo "Pipeline failed!"
      }
    }
  }
  ```
  
  **Topics covered:**
  - Pipeline overview and core concepts
  - Pipeline installation and setup
  - Creating pipeline jobs (script vs SCM)
  - Agent configuration (any, none, label, docker)
  - Post actions (always, success, failure, cleanup)
  - Stages and sequential execution
  - Steps and basic operations
  - Scripts and Groovy integration
  - Utility steps and file operations
  - Agent per stage configuration
  - Global variables and environment variables
  - Credentials management and binding
  - Options (timeout, retry, disable concurrent builds)
  - Parameters (string, choice, boolean, password)
  - Triggers (cron, pollSCM, upstream)
  - Input for manual approval
  - When conditions for conditional execution
  - Sequential and parallel stages
  - Matrix builds for multi-dimensional testing
  - Multi-branch pipeline for automatic branch detection

- 📙 **Jenkins Shared Library**

  Advanced guide for creating reusable pipeline code through shared libraries:
  
  ```groovy
  // Jenkinsfile using shared library
  @Library('jenkins-shared-library@main') _
  
  pipeline {
    agent any
    stages {
      stage("Use Shared Functions") {
        steps {
          script {
            hello.world()
            echo(author.name())
          }
        }
      }
    }
  }
  ```
  
  **Topics covered:**
  - Shared library overview and benefits
  - Creating shared library repository structure
  - Folder structure (vars/, src/, resources/)
  - Registering shared library in Jenkins
  - Using `@Library` annotation
  - Global variables and auto-import
  - Groovy classes and package structure
  - Parameters (string, list, map)
  - Library resources for non-code files
  - Declarative pipelines in shared libraries
  - Conditional pipeline logic

## 📍 References
- [Udemy](https://www.udemy.com/course/jenkins-pemula-sampai-mahir)

## 👨‍💻 Contributors
- [Dzaru Rizky Fathan Fortuna](https://www.linkedin.com/in/dzarurizky)
