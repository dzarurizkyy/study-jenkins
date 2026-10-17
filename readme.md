# Study Jenkins 🔧

This repository contains a comprehensive reference guide for Jenkins — covering installation and setup on a VPS, job management, Git and Slack integration, security, and distributed builds, then moving on to Continuous Delivery pipelines with the Groovy DSL and reusable pipeline code through Shared Libraries.

## Installation 🔧

1. **Clone the Repository**:

   ```bash
   git clone https://github.com/dzarurizkyy/study-jenkins.git
   cd study-jenkins
   ```

2. **Open the Guide**:
   - Start with [Jenkins Installation & Setup](001-jenkins-basics.md), then continue to [Jenkins Pipeline](002-jenkins-pipeline.md) and [Jenkins Shared Library](003-jenkins-shared-library.md)
   - Read them on GitHub, or open them in any Markdown viewer

<br />

   > **Note:** To follow along hands-on, you need a server or VPS with **Java 17**, **Git**, SSH access, and at least 256 MB RAM and 1 GB storage. The first guide walks through the full setup.

## List of Material 📚

- 📘 **[Jenkins Installation & Setup](001-jenkins-basics.md)**

  System requirements, VPS setup, Jenkins installation and initial configuration, process management, job management, Git integration with SSH keys, build steps, Slack notifications, triggers and scheduling, environment variables, build parameters, build history, executors, role-based security, custom views, distributed builds, and monitoring:

  ```bash
  # Install prerequisites
  sudo apt-get install git
  sudo apt install openjdk-17-jdk -y

  # Download and start Jenkins
  wget https://get.jenkins.io/war-stable/2.528.1/jenkins.war
  java -jar jenkins.war --httpPort=8080 &

  # Unlock Jenkins at http://YOUR_SERVER_IP:8080
  cat /root/.jenkins/secrets/initialAdminPassword
  ```

- 📗 **[Jenkins Pipeline](002-jenkins-pipeline.md)**

  Core concepts, pipeline jobs from script and SCM, agents, post actions, stages and steps, scripts, utility steps, agent per stage, global and environment variables, credentials, options, parameters, triggers, input, when conditions, sequential and parallel stages, matrix builds, credential binding, and multi-branch pipelines:

  ```groovy
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

- 📙 **[Jenkins Shared Library](003-jenkins-shared-library.md)**

  Folder structure (`vars/`, `src/`, `resources/`), registering a library in Jenkins, the `@Library` annotation, global variables, Groovy classes, string, list, and map parameters, library resources, and full declarative pipelines with conditional logic:

  ```text
  jenkins-shared-library/
  ├── resources/   → libraryResource("config/build.json")
  ├── src/         → import dzarurizkyy.jenkins.Output
  └── vars/        → hello.world(), author.name()
  ```

  Each project's `Jenkinsfile` only imports the library and calls its functions:

  ```groovy
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

## 📍 References

- [Udemy](https://www.udemy.com/course/jenkins-pemula-sampai-mahir)

## 👨‍💻 Contributors

- [Dzaru Rizky Fathan Fortuna](https://www.linkedin.com/in/dzarurizky)
