# ⚙️ Jenkins Hands-On Practice — CloudShop CI/CD Pipeline

A complete hands-on scenario that covers all core Jenkins concepts: installation, job management, Git integration, build configuration, post-build actions, triggers, environment variables, build parameters, distributed builds, Jenkins Pipeline (Declarative), shared libraries, and Docker integration — all in one real-world e-commerce CI/CD use case.

---

## 📖 Scenario

You are a DevOps engineer setting up a CI/CD pipeline for **CloudShop** — an e-commerce platform — using Jenkins. You will configure and automate:

- **Freestyle Jobs** — basic build and test automation
- **Git Integration** — pulling source code from GitHub
- **Slack Notifications** — post-build alerts to your team
- **Parameterized Builds** — deploy to different environments (dev, staging, prod)
- **Jenkins Pipeline** — declarative pipeline via `Jenkinsfile`
- **Shared Library** — reusable pipeline code across multiple projects
- **Distributed Builds** — running jobs on dedicated build agents

By the end of this practice, you will have hands-on experience with every concept covered in the Jenkins guides.

---

## 🗂️ Table of Contents

1. [Setup & Installation](#1-setup--installation)
2. [Initial Configuration](#2-initial-configuration)
3. [Freestyle Job Basics](#3-freestyle-job-basics)
4. [Git Integration](#4-git-integration)
5. [Build Configuration](#5-build-configuration)
6. [Post-Build Actions — Slack Notifications](#6-post-build-actions--slack-notifications)
7. [Triggers & Scheduling](#7-triggers--scheduling)
8. [Environment Variables](#8-environment-variables)
9. [Build Parameters](#9-build-parameters)
10. [Build History Management](#10-build-history-management)
11. [User Management & Security](#11-user-management--security)
12. [Custom Views](#12-custom-views)
13. [Distributed Builds](#13-distributed-builds)
14. [Jenkins Pipeline — Basics](#14-jenkins-pipeline--basics)
15. [Pipeline with SCM (Jenkinsfile)](#15-pipeline-with-scm-jenkinsfile)
16. [Pipeline Advanced Features](#16-pipeline-advanced-features)
17. [Shared Library](#17-shared-library)
18. [Multi-Branch Pipeline](#18-multi-branch-pipeline)
19. [Utilities & Cleanup](#19-utilities--cleanup)
20. [Challenge Tasks](#-challenge-tasks)

---

## 1. Setup & Installation

### 1a. Prepare the VPS

Connect to your VPS via SSH:

```bash
ssh root@YOUR_PUBLIC_IP
```

### 1b. Install Prerequisites

```bash
# Install Git
sudo apt-get install git -y
git --version

# Install Java 17
sudo apt install openjdk-17-jdk -y
javac --version
```

### 1c. Download and Start Jenkins

```bash
# Download Jenkins WAR
wget https://get.jenkins.io/war-stable/2.528.1/jenkins.war

# Start Jenkins on port 8080
java -jar jenkins.war --httpPort=8080 &
```

### 1d. Get Your VPS IP and Access Jenkins

```bash
hostname -I
```

Open your browser at `http://YOUR_VPS_IP:8080`.

> 💡 Jenkins runs in the background with `&`. To stop it later, use `pkill -f jenkins.war`.

---

## 2. Initial Configuration

### 2a. Unlock Jenkins

```bash
cat /root/.jenkins/secrets/initialAdminPassword
```

Copy the output and paste it into the Jenkins unlock page in your browser.

### 2b. Skip Plugin Installation (Minimal Setup)

When presented with plugin options:
- Select **"Select plugins to install"**
- Click **"None"** to deselect all
- Click **"Next"**

> We will install only what we need step by step.

### 2c. Create Admin User

Fill in your admin account details:
- **Username:** `admin`
- **Password:** A strong password
- **Email:** your email address

Click **"Save and Finish"** → **"Start using Jenkins"**.

### 2d. Verify Jenkins is Running

```bash
ps aux | grep jenkins
# Should show the Java process running jenkins.war
```

---

## 3. Freestyle Job Basics

### 3a. Create Your First Job

- Go to Jenkins dashboard
- Click **"New Item"**
- Enter job name: `cloudshop-hello`
- Select **"Freestyle project"**
- Click **"OK"**

### 3b. Add a Build Step

- Scroll to **"Build Steps"**
- Click **"Add build step"** → **"Execute shell"**
- Enter:

```bash
echo "CloudShop CI/CD Pipeline is alive!"
echo "Build Number: $BUILD_NUMBER"
echo "Job Name: $JOB_NAME"
```

- Click **"Save"**

### 3c. Run the Job

- Click **"Build Now"**
- Watch the build appear in **"Build History"**

### 3d. View Console Output

- Click on the build number (e.g., `#1`)
- Select **"Console Output"**
- Verify you see:

```
CloudShop CI/CD Pipeline is alive!
Build Number: 1
Job Name: cloudshop-hello
```

### 3e. Delete the Practice Job

- Go to the job page
- Click **"Delete Project"** → **"Yes"**

---

## 4. Git Integration

### 4a. Install the Git Plugin

- Go to **Manage Jenkins** → **Plugins** → **Available plugins**
- Search **"Git Plugin"**
- Check the installation box
- Check **"Restart Jenkins when installation is complete and no jobs are running"**

### 4b. Generate SSH Key on the VPS

```bash
ssh-keygen
cd ~/.ssh/
cat id_rsa.pub
```

Copy the entire public key output.

### 4c. Add SSH Key to GitHub

- Go to GitHub → **Settings** → **SSH and GPG keys**
- Click **"New SSH key"**
- **Title:** `cloudshop-jenkins-vps`
- **Key:** Paste your public key
- Click **"Add SSH key"**

### 4d. Add SSH Credentials in Jenkins

- Go to Jenkins → **Profile** → **Credentials**
- Click **"Global credentials"** → **"Add Credentials"**
- **Kind:** `SSH Username with private key`
- **ID:** `github-ssh-key`
- **Description:** `GitHub SSH Key for CloudShop`
- **Username:** Your GitHub username
- **Private Key:** Select **"Enter directly"**, then paste:

```bash
cat ~/.ssh/id_rsa
```

- Click **"Create"**

### 4e. Create a Job with Git Source

- Create new job: `cloudshop-backend`
- **Freestyle project** → **"OK"**
- Scroll to **"Source Code Management"** → Select **"Git"**
- **Repository URL:** Your GitHub repository SSH URL
- **Credentials:** Select `github-ssh-key`
- **Branch Specifier:** `*/main`
- Click **"Save"** → **"Build Now"**

### 4f. Verify Checkout in Console Output

Click on the build and view **"Console Output"**. You should see:

```
Cloning the remote Git repository
Cloning repository git@github.com:YOUR_USERNAME/YOUR_REPO.git
...
Checking out Revision ...
```

---

## 5. Build Configuration

### 5a. Add Build Steps to `cloudshop-backend`

- Open job → **"Configure"** → **"Build Steps"**
- Click **"Add build step"** → **"Execute shell"**
- Enter:

```bash
echo "=== CloudShop Backend Build ==="
echo "Branch: $GIT_BRANCH"
echo "Commit: $GIT_COMMIT"

# Install dependencies (example for Node.js project)
# npm install

# Run tests
# npm test

# Build application
# npm run build

echo "=== Build Complete ==="
```

- Click **"Save"**

### 5b. Add Multiple Build Steps

Click **"Add build step"** again to add a second shell step:

```bash
echo "=== Post-Build Verification ==="
echo "Workspace: $WORKSPACE"
ls -la $WORKSPACE
echo "=== Verification Complete ==="
```

### 5c. Build and Review Output

Click **"Build Now"** and check the **Console Output** to see both steps execute sequentially.

---

## 6. Post-Build Actions — Slack Notifications

### 6a. Install Slack Notification Plugin

- Go to **Manage Jenkins** → **Plugins** → **Available plugins**
- Search **"Slack Notification Plugin"**
- Install and restart Jenkins

### 6b. Configure Slack Integration

- Navigate to `https://my.slack.com/services/new/jenkins-ci`
- Select or create a channel: `#cloudshop-ci`
- Click **"Add Jenkins CI integration"**
- Note the **Team Subdomain** and **Integration Token**

### 6c. Add Slack Token as Jenkins Credential

- Go to **Credentials** → **Global credentials** → **"Add Credentials"**
- **Kind:** `Secret text`
- **Secret:** Paste your Slack Integration Token
- **ID:** `slack-token`
- **Description:** `Slack Integration Token`
- Click **"Create"**

### 6d. Configure Slack in System Settings

- Go to **Manage Jenkins** → **System**
- Scroll to **"Slack"** section
- **Workspace:** Your Slack Team Subdomain
- **Credential:** Select `slack-token`
- **Default channel:** `#cloudshop-ci`
- Click **"Save"**

### 6e. Add Slack Notification to Job

- Open `cloudshop-backend` → **"Configure"**
- Scroll to **"Post-build Actions"**
- Click **"Add post-build action"** → **"Slack Notifications"**
- Check: **Notify Build Start**, **Notify Success**, **Notify Failure**
- Click **"Save"**

### 6f. Test the Integration

Click **"Build Now"** and verify notifications appear in `#cloudshop-ci` on Slack.

---

## 7. Triggers & Scheduling

### 7a. Schedule a Nightly Build

- Open `cloudshop-backend` → **"Configure"**
- Scroll to **"Build Triggers"**
- Check **"Build periodically"**
- Enter schedule:

```
# Every day at midnight
0 0 * * *
```

- Click **"Save"**

> 💡 Use [crontab.guru](https://crontab.guru/) to build and validate cron expressions.

### 7b. Poll SCM for Changes

- Open job → **"Configure"** → **"Build Triggers"**
- Check **"Poll SCM"**
- Enter: `* * * * *` (checks every minute)
- Click **"Save"**

Jenkins will only trigger a build when it detects new commits in the repository.

### 7c. Enable Remote Trigger

- Open job → **"Configure"** → **"Build Triggers"**
- Check **"Trigger builds remotely"**
- **Authentication Token:** `cloudshop-remote-token`
- Click **"Save"**

Trigger a build from a terminal:

```bash
curl "http://YOUR_JENKINS_IP:8080/job/cloudshop-backend/build?token=cloudshop-remote-token"
```

---

## 8. Environment Variables

### 8a. View Available Environment Variables

Open your browser and navigate to:

```
http://YOUR_JENKINS_IP:8080/env-vars.html/
```

Browse the full list of built-in Jenkins environment variables.

### 8b. Use Built-in Variables in a Build Step

- Create new job: `cloudshop-env-demo`
- Add **"Execute shell"** build step:

```bash
echo "===== Build Info ====="
echo "Job Name:      ${JOB_NAME}"
echo "Build Number:  ${BUILD_NUMBER}"
echo "Build URL:     ${BUILD_URL}"
echo "Workspace:     ${WORKSPACE}"
echo "Git Branch:    ${GIT_BRANCH}"
echo "======================"
```

- Click **"Save"** → **"Build Now"**
- Check **Console Output** for the values.

### 8c. Create Global Environment Variables

- Go to **Manage Jenkins** → **System**
- Scroll to **"Global properties"**
- Check **"Environment variables"**
- Click **"Add"**:

| Name | Value |
|------|-------|
| `APP_NAME` | `CloudShop` |
| `AUTHOR` | `Your Name` |
| `DEPLOY_USER` | `deploy` |

- Click **"Save"**

### 8d. Use Global Variables

- Open `cloudshop-env-demo` → **"Configure"** → add another build step:

```bash
echo "App: ${APP_NAME}"
echo "Author: ${AUTHOR}"
echo "Deploy User: ${DEPLOY_USER}"
```

- Build and verify all variables appear in **Console Output**.

---

## 9. Build Parameters

### 9a. Add Parameters to the Deployment Job

- Create new job: `cloudshop-deploy`
- Check **"This project is parameterized"**
- Click **"Add Parameter"** → **"Choice Parameter"**:
  - **Name:** `ENVIRONMENT`
  - **Choices** (one per line):
    ```
    development
    staging
    production
    ```
  - **Description:** `Target deployment environment`

- Add another parameter → **"String Parameter"**:
  - **Name:** `VERSION`
  - **Default Value:** `1.0.0`
  - **Description:** `Application version to deploy`

- Add another parameter → **"Boolean Parameter"**:
  - **Name:** `RUN_TESTS`
  - **Default Value:** ✅ checked
  - **Description:** `Run tests before deploying`

### 9b. Use Parameters in Build Steps

- Scroll to **"Build Steps"** → **"Execute shell"**:

```bash
echo "===== CloudShop Deployment ====="
echo "Environment: ${ENVIRONMENT}"
echo "Version:     ${VERSION}"
echo "Run Tests:   ${RUN_TESTS}"

if [ "${RUN_TESTS}" = "true" ]; then
  echo "Running test suite..."
  # npm test
fi

echo "Deploying CloudShop v${VERSION} to ${ENVIRONMENT}..."
# ./deploy.sh ${ENVIRONMENT} ${VERSION}

echo "Deployment complete!"
```

- Click **"Save"**

### 9c. Build with Parameters

- Click **"Build with Parameters"**
- Select `ENVIRONMENT`: `staging`
- Set `VERSION`: `2.0.1`
- Check `RUN_TESTS`: ✅
- Click **"Build"**

Verify the parameter values appear correctly in **Console Output**.

---

## 10. Build History Management

### 10a. Configure Log Rotation

- Open `cloudshop-backend` → **"Configure"**
- Check **"Discard old builds"**
- **Strategy:** `Log Rotation`
- **Days to keep builds:** `7`
- **Max # of builds to keep:** `10`
- Click **"Save"**

Jenkins will automatically prune old builds after each new build completes.

### 10b. Disable a Job

- Open a job → **"Configure"**
- Toggle switch on the right: change to **"Disabled"**
- Click **"Save"**

The **"Build Now"** button will disappear from the job page.

### 10c. Copy a Job

- Dashboard → **"New Item"**
- Enter name: `cloudshop-deploy-copy`
- In **"Copy from"** field: `cloudshop-deploy`
- Click **"OK"**, modify as needed, then **"Save"**

### 10d. Re-enable the Job

- Open the disabled job → **"Configure"**
- Toggle switch back to **"Enabled"**
- Click **"Save"**

---

## 11. User Management & Security

### 11a. Create a New User

- Go to **Manage Jenkins** → **Users** → **"Create User"**
- **Username:** `developer1`
- **Password:** A strong password
- **Full name:** `Developer One`
- **Email:** developer1@cloudshop.com
- Click **"Create User"**

### 11b. Test the New User

- Sign out of Jenkins
- Log in as `developer1`
- Verify basic access

### 11c. Allow Anonymous Read Access

- Go to **Manage Jenkins** → **Security**
- **Authorization:** Select **"Logged-in users can do anything"**
- Check **"Allow anonymous read access"**
- Click **"Save"**

Open an incognito window and navigate to your Jenkins URL — you should be able to see jobs without logging in.

### 11d. Install Role-Based Authorization Plugin

- Go to **Plugins** → **Available plugins**
- Search **"Role-based Authorization Strategy"**
- Install and restart Jenkins

### 11e. Enable Role-Based Strategy

- Go to **Manage Jenkins** → **Security**
- **Authorization:** Select **"Role-Based Strategy"**
- Click **"Save"**

### 11f. Create Roles

- Go to **Manage Jenkins** → **Manage and Assign Roles** → **Manage Roles**

Add the following roles:

| Role | Overall: Read | Job: Read | Job: Build | Manage Jenkins |
|------|:---:|:---:|:---:|:---:|
| `admin` | ✅ | ✅ | ✅ | ✅ |
| `developer` | ✅ | ✅ | ✅ | ❌ |
| `viewer` | ✅ | ✅ | ❌ | ❌ |

- Click **"Save"**

### 11g. Assign Roles to Users

- Go to **Manage and Assign Roles** → **Assign Roles**
- Assign `developer` role to `developer1`
- Click **"Save"**

Sign in as `developer1` and verify they can build jobs but cannot access **Manage Jenkins**.

---

## 12. Custom Views

### 12a. Create a Team View

- On the Jenkins dashboard, click **"+"** (next to "All" tab)
- **View name:** `CloudShop-Backend`
- Select **"List View"**
- Click **"Create"**

### 12b. Configure the View

**Jobs section:** Select:
- `cloudshop-backend`
- `cloudshop-deploy`

**Columns section:** Keep:
- Status
- Weather
- Name
- Last Success
- Last Failure
- Last Duration
- Build Button

Click **"OK"**.

### 12c. Create an Environment View

Repeat to create a second view named `CloudShop-Frontend`, selecting only frontend-related jobs.

### 12d. Switch Between Views

Click the view tabs on the Jenkins dashboard to filter the job list.

> 💡 **View use cases:**
> - **Team Views** — Group jobs by team (backend, frontend, infra)
> - **Environment Views** — Separate dev, staging, and production jobs
> - **Status Views** — Highlight failing or unstable jobs

---

## 13. Distributed Builds

### 13a. Install SSH Build Agents Plugin

- Go to **Plugins** → **Available plugins**
- Search **"SSH Build Agents"**
- Install and restart Jenkins

### 13b. Prepare the Agent VPS

On your **second VPS** (not the Jenkins master):

```bash
# Install Git
sudo apt-get install git -y

# Install Java 17 (same version as master)
sudo apt install openjdk-17-jdk -y

# Get the working directory
pwd
# Note this path — you'll need it when configuring the node
```

> ⚠️ Do **NOT** install Jenkins on the agent node.

### 13c. Create a New Node

- Go to **Manage Jenkins** → **Nodes** → **"New Node"**
- **Node name:** `cloudshop-agent-01`
- Select **"Permanent Agent"**
- Click **"Create"**

### 13d. Configure the Node

- **# of executors:** `2`
- **Remote root directory:** Path from `pwd` on the agent VPS
- **Launch method:** `Launch agents via SSH`
- **Host:** Agent VPS IP address
- **Credentials:** Add new → **"Username with password"**
  - **Username:** Agent VPS username
  - **Password:** Agent VPS SSH password
  - **ID:** `agent-01-credentials`
- Click **"Save"**

### 13e. Verify Connection

If successful, the node status page shows:

```
Agent successfully connected and online
```

### 13f. Add Labels to the Agent

- Go to **Manage Jenkins** → **Nodes** → click on `cloudshop-agent-01`
- Click **"Configure"**
- **Labels:** `linux java17 cloudshop`
- Click **"Save"**

### 13g. Build Using the Agent

- Open any job → **"Configure"**
- Check **"Restrict where this project can be run"**
- **Label Expression:** `linux && java17`
- Click **"Save"** → **"Build Now"**

Verify in **Console Output** that the build ran on `cloudshop-agent-01`.

---

## 14. Jenkins Pipeline — Basics

### 14a. Install the Pipeline Plugin

- Go to **Plugins** → **Available plugins**
- Search **"Pipeline: Aggregator"**
- Install and restart Jenkins

### 14b. Create Your First Pipeline Job

- Go to dashboard → **"New Item"**
- **Name:** `cloudshop-pipeline-hello`
- Select **"Pipeline"** → **"OK"**
- Scroll to **"Pipeline"** section
- Select **"Pipeline script"**

### 14c. Write a Basic Pipeline

```groovy
pipeline {
  agent any
  stages {
    stage("Checkout") {
      steps {
        echo "Checking out CloudShop source code..."
      }
    }
    stage("Build") {
      steps {
        echo "Building CloudShop application..."
        sleep(3)
        echo "Build complete!"
      }
    }
    stage("Test") {
      steps {
        echo "Running CloudShop test suite..."
        sleep(2)
        echo "All tests passed!"
      }
    }
    stage("Deploy") {
      steps {
        echo "Deploying to staging environment..."
        echo "Deployment complete!"
      }
    }
  }
}
```

- Click **"Save"** → **"Build Now"**

### 14e. Install Pipeline Stage View Plugin

- Go to **Plugins** → **Available plugins**
- Install **"Pipeline: Stage View"**
- After installation, return to the job page to see the visual stage grid.

### 14f. Add Post Actions

Edit the pipeline script:

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
  }
  post {
    always {
      echo "Pipeline finished. Cleaning up workspace..."
    }
    success {
      echo "✅ CloudShop pipeline succeeded!"
    }
    failure {
      echo "❌ CloudShop pipeline failed! Notifying team..."
    }
  }
}
```

---

## 15. Pipeline with SCM (Jenkinsfile)

### 15a. Create a Jenkinsfile in Your Repository

In your GitHub repository, create a file at the root named `Jenkinsfile`:

```groovy
pipeline {
  agent any

  environment {
    APP_NAME = "CloudShop"
    DEPLOY_ENV = "staging"
  }

  stages {
    stage("Checkout") {
      steps {
        echo "Source code checked out by Jenkins SCM"
        echo "Branch: ${env.GIT_BRANCH}"
      }
    }

    stage("Build") {
      steps {
        echo "Building ${APP_NAME}..."
        sh "echo 'build output' > build.log"
      }
    }

    stage("Test") {
      steps {
        echo "Running tests for ${APP_NAME}..."
        script {
          def testResults = ["unit", "integration", "smoke"]
          for (test in testResults) {
            echo "Running ${test} tests... PASSED"
          }
        }
      }
    }

    stage("Deploy") {
      steps {
        echo "Deploying ${APP_NAME} to ${DEPLOY_ENV}..."
      }
    }
  }

  post {
    always {
      echo "Build #${env.BUILD_NUMBER} of ${APP_NAME} completed"
    }
    success {
      echo "✅ Deployed successfully to ${DEPLOY_ENV}"
    }
    failure {
      echo "❌ Build failed — check logs at ${env.BUILD_URL}"
    }
  }
}
```

Commit and push:

```bash
git add Jenkinsfile
git commit -m "Add Jenkins pipeline"
git push origin main
```

### 15b. Create a Pipeline Job from SCM

- Dashboard → **"New Item"** → `cloudshop-pipeline-scm` → **"Pipeline"** → **"OK"**
- Scroll to **"Pipeline"** → **Definition:** `Pipeline script from SCM`
- **SCM:** `Git`
- **Repository URL:** Your GitHub repository URL
- **Credentials:** `github-ssh-key`
- **Branch Specifier:** `*/main`
- **Script Path:** `Jenkinsfile`
- Click **"Save"** → **"Build Now"**

Verify the pipeline reads and executes the `Jenkinsfile` from your repository.

---

## 16. Pipeline Advanced Features

### 16a. Add Pipeline Options and Timeouts

Update your `Jenkinsfile`:

```groovy
pipeline {
  agent any

  options {
    disableConcurrentBuilds()
    timeout(time: 15, unit: 'MINUTES')
    timestamps()
  }

  stages {
    stage("Build") {
      steps {
        echo "Building with timeout and timestamps enabled"
      }
    }
  }
}
```

### 16b. Add Pipeline Parameters

```groovy
pipeline {
  agent any

  parameters {
    choice(
      name: "ENVIRONMENT",
      choices: ["development", "staging", "production"],
      description: "Target deployment environment"
    )
    string(
      name: "VERSION",
      defaultValue: "1.0.0",
      description: "Release version"
    )
    booleanParam(
      name: "SKIP_TESTS",
      defaultValue: false,
      description: "Skip test stage?"
    )
  }

  stages {
    stage("Build") {
      steps {
        echo "Building CloudShop v${params.VERSION}"
      }
    }

    stage("Test") {
      when {
        expression { return !params.SKIP_TESTS }
      }
      steps {
        echo "Running tests..."
      }
    }

    stage("Deploy") {
      steps {
        echo "Deploying v${params.VERSION} to ${params.ENVIRONMENT}"
      }
    }
  }
}
```

### 16c. Add a Manual Approval Gate

```groovy
stage("Deploy to Production") {
  input {
    message "Deploy CloudShop to PRODUCTION?"
    ok "Yes, deploy now"
    submitter "admin"
    parameters {
      choice(
        name: "CONFIRM_ENV",
        choices: ["production"],
        description: "Confirm target environment"
      )
    }
  }
  steps {
    echo "Deploying to ${CONFIRM_ENV}..."
  }
}
```

### 16d. Add Parallel Stages

```groovy
stage("Test in Parallel") {
  parallel {
    stage("Unit Tests") {
      agent {
        node { label "linux && java17" }
      }
      steps {
        echo "Running unit tests on agent..."
      }
    }
    stage("Integration Tests") {
      agent {
        node { label "linux && java17" }
      }
      steps {
        echo "Running integration tests on agent..."
        sleep(5)
      }
    }
    stage("Lint & Code Quality") {
      agent {
        node { label "linux && java17" }
      }
      steps {
        echo "Running linter and quality checks..."
      }
    }
  }
}
```

### 16e. Use Credentials Securely

Add credentials to Jenkins first, then reference them in the pipeline:

```groovy
pipeline {
  agent any

  environment {
    // Automatically creates DEPLOY_CREDS_USR and DEPLOY_CREDS_PSW variables
    DEPLOY_CREDS = credentials("deploy-server-credentials")
  }

  stages {
    stage("Deploy") {
      steps {
        echo "Deploying as user: ${DEPLOY_CREDS_USR}"
        // Use single quotes to prevent Groovy interpolation of secrets
        sh('echo "Connecting to server with $DEPLOY_CREDS_PSW" > deploy.log')
      }
    }
  }
}
```

> ⚠️ Always use **single quotes** `''` with `$VAR` for sensitive credential values — never double quotes `""` with `${VAR}`.

---

## 17. Shared Library

### 17a. Create the Shared Library Repository

Create a new GitHub repository named `cloudshop-jenkins-library` with this folder structure:

```
cloudshop-jenkins-library/
├── resources/
│   └── config/
│       └── app-config.json
├── src/
│   └── com/
│       └── cloudshop/
│           └── Deploy.groovy
└── vars/
    ├── buildInfo.groovy
    ├── cloudshopPipeline.groovy
    └── notify.groovy
```

### 17b. Create `vars/buildInfo.groovy`

```groovy
def call() {
  "CloudShop Build System v1.0"
}

def appName() {
  "CloudShop"
}

def printInfo() {
  echo("=== Build Info ===")
  echo("App: ${appName()}")
  echo("Build: ${env.BUILD_NUMBER}")
  echo("Branch: ${env.GIT_BRANCH}")
  echo("==================")
}
```

### 17c. Create `vars/notify.groovy`

```groovy
def success(String env) {
  echo("✅ CloudShop deployed successfully to ${env}")
}

def failure(String reason) {
  echo("❌ CloudShop build failed: ${reason}")
}

def call(String message) {
  echo("📢 Notification: ${message}")
}
```

### 17d. Create `vars/cloudshopPipeline.groovy`

```groovy
def call(Map config) {
  pipeline {
    agent any

    stages {
      stage("Info") {
        steps {
          script {
            buildInfo.printInfo()
          }
        }
      }

      stage("Build") {
        steps {
          echo("Building ${buildInfo.appName()} version ${config.version ?: '1.0.0'}")
        }
      }

      stage("Deploy") {
        steps {
          echo("Deploying to ${config.environment ?: 'staging'}")
          script {
            notify.success(config.environment ?: 'staging')
          }
        }
      }
    }

    post {
      failure {
        script {
          notify.failure("Check build #${env.BUILD_NUMBER}")
        }
      }
    }
  }
}
```

### 17e. Create `resources/config/app-config.json`

```json
{
  "app": "CloudShop",
  "team": "DevOps",
  "repository": "github.com/your-org/cloudshop"
}
```

### 17f. Create `src/com/cloudshop/Deploy.groovy`

```groovy
package com.cloudshop

class Deploy {
  static def run(steps, String environment, String version) {
    steps.echo("Deploying CloudShop v${version} to ${environment}")
    steps.sh("echo 'deploy ${version} ${environment}' > deploy.log")
  }
}
```

Commit and push the shared library to GitHub.

### 17g. Register the Shared Library in Jenkins

- Go to **Manage Jenkins** → **System**
- Scroll to **"Global Trusted Pipeline Libraries"**
- Click **"Add"**:
  - **Name:** `cloudshop-jenkins-library`
  - **Default version:** `main`
  - **Retrieval method:** `Modern SCM`
  - **Source Control:** `Git`
  - **Repository URL:** Your library GitHub repo URL
  - **Credentials:** `github-ssh-key`
- Click **"Save"**

### 17h. Use the Shared Library in a Jenkinsfile

In your main project repository, update `Jenkinsfile`:

```groovy
@Library('cloudshop-jenkins-library@main') _

cloudshopPipeline([
  environment: "staging",
  version: "2.1.0"
])
```

Commit, push, and trigger the pipeline. Verify all shared library functions execute correctly.

### 17i. Use Individual Library Functions

```groovy
@Library('cloudshop-jenkins-library@main') _

import com.cloudshop.Deploy

pipeline {
  agent any

  stages {
    stage("Info") {
      steps {
        script {
          echo(buildInfo())
          buildInfo.printInfo()
        }
      }
    }

    stage("Load Config") {
      steps {
        script {
          def config = libraryResource("config/app-config.json")
          echo(config)
        }
      }
    }

    stage("Deploy") {
      steps {
        script {
          Deploy.run(this, "staging", "2.1.0")
          notify.success("staging")
        }
      }
    }
  }
}
```

---

## 18. Multi-Branch Pipeline

### 18a. Create Feature Branches in Your Repository

```bash
# Create a develop branch
git checkout -b develop
git push origin develop

# Create a feature branch
git checkout -b feature/checkout-page
git push origin feature/checkout-page
```

### 18b. Create a Multi-Branch Pipeline Job

- Dashboard → **"New Item"**
- **Name:** `cloudshop-multibranch`
- Select **"Multi-branch Pipeline"** → **"OK"**

### 18c. Configure Branch Sources

- Click **"Add source"** → **"Git"**
- **Repository URL:** Your GitHub repository URL
- **Credentials:** `github-ssh-key`

### 18d. Configure Build Configuration

- **Mode:** `by Jenkinsfile`
- **Script Path:** `Jenkinsfile`

### 18e. Configure Scan Triggers

- Under **"Scan Multi-branch Pipeline Triggers"**
- Check **"Periodically if not otherwise run"**
- **Interval:** `1 minute`

### 18f. Save and Scan

- Click **"Save"**
- Jenkins scans the repository and creates a job for each branch that contains a `Jenkinsfile`

Verify you see separate job entries for `main`, `develop`, and `feature/checkout-page`.

### 18g. Test Branch Isolation

- Make a commit on the `feature/checkout-page` branch
- Verify only the feature branch job triggers, not main or develop

---

## 19. Utilities & Cleanup

### 19a. View Build Trends

- Open any job
- Find the **"Build History"** section
- Click **"Expand"** to see a build time trend graph

### 19b. Check Build Executors

- Go to **Manage Jenkins** → **System**
- Scroll to **"# of executors"**
- Set to `4` for the master node
- Click **"Save"**

### 19c. Monitor Resource Usage

On the VPS:

```bash
# Check Jenkins memory usage
ps aux | grep jenkins | awk '{print $1, $2, $3, $4, $11}'

# Check disk usage of Jenkins home directory
du -sh ~/.jenkins/
du -sh ~/.jenkins/jobs/
du -sh ~/.jenkins/workspace/
```

### 19d. Stop and Restart Jenkins

```bash
# Find Jenkins PID
ps aux | grep jenkins

# Kill Jenkins process
pkill -f jenkins.war

# Restart Jenkins
java -jar jenkins.war --httpPort=8080 &
```

### 19e. Manage Jenkins Home Directory

```bash
cd ~/.jenkins/

# View structure
ll

# View jobs
ls jobs/

# View workspace for a specific job
ls workspace/cloudshop-backend/
```

---

## 🏆 Challenge Tasks

Once you have completed the practice above, try these on your own:

---

### Challenge 1 — Freestyle Job Chaining

Create three freestyle jobs: `cloudshop-build`, `cloudshop-test`, and `cloudshop-release`. Configure them so that a successful build of `cloudshop-build` automatically triggers `cloudshop-test`, and a successful test triggers `cloudshop-release`. Verify the chain by triggering `cloudshop-build` manually.

---

### Challenge 2 — Environment-Specific Configurations

Create a parameterized job with a `ENVIRONMENT` choice parameter (`dev`, `staging`, `prod`). Using shell script logic, print a different deployment message and simulate a different set of steps for each environment. For `prod`, add a `sleep 5` to simulate a longer deployment.

---

### Challenge 3 — Build History & Discard Policy

Create a job that generates a build artifact using:

```bash
echo "Build ${BUILD_NUMBER} artifact" > artifact-${BUILD_NUMBER}.txt
```

Configure a log rotation policy to keep only the last 5 builds. Run the job 8 times and verify only 5 remain.

---

### Challenge 4 — Role-Based Access Control

Create three users: `alice` (admin role), `bob` (developer role), and `carol` (viewer role). Log in as each and verify:
- Alice can access Manage Jenkins
- Bob can build jobs but cannot access Manage Jenkins
- Carol can view jobs but cannot build them

---

### Challenge 5 — Distributed Build with Labels

Set up a second Jenkins agent with labels `linux java17 staging`. Create a pipeline with three stages, each requiring a different label:
- Stage 1: runs on `linux && java17`
- Stage 2: runs on `linux && staging`
- Stage 3: runs on any agent

Run the pipeline and verify each stage executes on the correct agent from the Console Output.

---

### Challenge 6 — Full Declarative Pipeline

Write a complete `Jenkinsfile` for the CloudShop project that includes:
- `options` block with `disableConcurrentBuilds()` and a 10-minute `timeout`
- `environment` block with `APP_NAME` and `VERSION`
- `parameters` block with `ENVIRONMENT` (choice) and `SKIP_TESTS` (boolean)
- A `when` condition on the test stage based on `SKIP_TESTS`
- A `post` block with `always`, `success`, and `failure` handlers
- At least 4 stages: Checkout, Build, Test, Deploy

---

### Challenge 7 — Parallel Test Matrix

Write a pipeline with a single `stage("Test Matrix")` that runs 4 parallel sub-stages simultaneously:
- `Unit Tests`
- `Integration Tests`
- `Performance Tests`
- `Security Scan`

Each sub-stage should print its name and sleep for a different duration (2s, 4s, 3s, 5s). Add `parallelAlwaysFailFast()` to options and deliberately fail one stage to observe the behavior.

---

### Challenge 8 — Matrix Build

Write a pipeline using `matrix` to build CloudShop across two axes:
- `ENVIRONMENT`: `dev`, `staging`, `prod`
- `REGION`: `us-east`, `eu-west`

Exclude the combination of `prod` + `us-east`. The matrix should produce 5 parallel cells. Each cell should print its `${ENVIRONMENT}-${REGION}` combination.

---

### Challenge 9 — Shared Library Extension

Extend the `cloudshop-jenkins-library` shared library with:
1. A new `vars/healthCheck.groovy` with a `call(String url)` function that prints a simulated HTTP check message
2. A `vars/deploymentReport.groovy` with a `generate(Map info)` function that echoes a formatted deployment summary
3. Use both functions in a new Jenkinsfile in your project

Tag the library with `v1.0.0` and update the `@Library` annotation in your Jenkinsfile to use `@Library('cloudshop-jenkins-library@v1.0.0')`.

---

### Challenge 10 — Complete Multi-Branch + Shared Library

Create a repository with three branches: `main`, `release/2.0`, and `develop`. Each branch should have a `Jenkinsfile` that:
- Uses the shared library via `@Library`
- Defines a `DEPLOY_ENV` environment variable based on the branch name (`main` → `production`, `release/*` → `staging`, `develop` → `development`)
- Runs a different set of stages depending on the branch using `when { branch '...' }`

Set up a Multi-Branch Pipeline job to scan the repository and verify all three branches create separate jobs with the correct behavior.

---

## ✅ Concepts Covered

| Concept | Where Practiced |
|---|---|
| VPS SSH setup | Step 1a |
| Java + Git prerequisites | Step 1b |
| Jenkins WAR installation | Step 1c |
| Initial admin unlock | Step 2a |
| Plugin installation | Step 2b, 4a |
| Freestyle job creation | Step 3a–3c |
| Console output inspection | Step 3d |
| SSH key generation | Step 4b |
| GitHub SSH key setup | Step 4c |
| SSH credentials in Jenkins | Step 4d |
| Git SCM integration | Step 4e–4f |
| Multi-step build configuration | Step 5a–5c |
| Slack plugin setup | Step 6a–6c |
| Slack system configuration | Step 6d |
| Post-build Slack notifications | Step 6e–6f |
| Periodic build scheduling (cron) | Step 7a |
| SCM polling | Step 7b |
| Remote build trigger | Step 7c |
| Built-in environment variables | Step 8a–8b |
| Global environment variables | Step 8c–8d |
| Choice parameter | Step 9a |
| String and boolean parameters | Step 9a |
| Build with parameters | Step 9c |
| Log rotation policy | Step 10a |
| Disable/enable job | Step 10b |
| Copy job | Step 10c |
| Create users | Step 11a–11b |
| Anonymous read access | Step 11c |
| Role-based authorization plugin | Step 11d–11e |
| Create and assign roles | Step 11f–11g |
| List view creation | Step 12a–12b |
| View column configuration | Step 12b |
| SSH Build Agents plugin | Step 13a |
| Agent node setup | Step 13c–13e |
| Node labeling | Step 13f |
| Restrict job to labeled agent | Step 13g |
| Pipeline plugin installation | Step 14a |
| Basic declarative pipeline | Step 14c |
| Post conditions (always, success, failure) | Step 14f |
| Pipeline script from SCM | Step 15b |
| Jenkinsfile in repository | Step 15a |
| `disableConcurrentBuilds()`, `timeout()` | Step 16a |
| Pipeline parameters | Step 16b |
| `when` conditions | Step 16b |
| `input` for manual approval | Step 16c |
| `parallel` stages | Step 16d |
| `credentials()` in environment | Step 16e |
| Single-quote vs double-quote for secrets | Step 16e |
| Shared library folder structure | Step 17a |
| `vars/` global variables | Step 17b–17d |
| `resources/` library files | Step 17e |
| `src/` Groovy classes | Step 17f |
| Register library in Jenkins | Step 17g |
| `@Library` annotation | Step 17h |
| `libraryResource()` function | Step 17i |
| `call()` default function | Step 17d |
| Multi-branch pipeline creation | Step 18b–18f |
| Branch isolation | Step 18g |
| Build trend graph | Step 19a |
| Executor configuration | Step 19b |
| Jenkins process management | Step 19d |
| Jenkins home directory structure | Step 19e |
