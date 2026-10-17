# ⚙️ Jenkins Installation & Setup

A practical reference guide for installing and configuring Jenkins on a VPS from scratch — covering installation, job management, Git integration, notifications, triggers, parameters, security, and distributed builds.

---

## 📋 Table of Contents

- [Introduction](#-introduction)
  - [Who Is This For?](#who-is-this-for)
  - [What This Material Covers](#what-this-material-covers)
  - [Why Learn Jenkins?](#why-learn-jenkins)
- [System Requirements](#-system-requirements)
  - [Minimum Requirements](#minimum-requirements)
  - [Recommended Requirements](#recommended-requirements)
  - [Current Setup (Hostinger KVM 2)](#current-setup-hostinger-kvm-2)
- [VPS Setup](#️-vps-setup)
  - [Prepare the VPS](#prepare-the-vps)
  - [Connect via SSH](#connect-via-ssh)
  - [Generate SSH Key](#generate-ssh-key)
- [Prerequisites Installation](#-prerequisites-installation)
  - [Install Git](#install-git)
  - [Install Java 17](#install-java-17)
- [Jenkins Installation](#-jenkins-installation)
  - [Download Jenkins](#download-jenkins)
  - [Start Jenkins](#start-jenkins)
  - [Access the Jenkins Web Interface](#access-the-jenkins-web-interface)
- [Initial Configuration](#-initial-configuration)
  - [Unlock Jenkins](#unlock-jenkins)
  - [Create Admin User](#create-admin-user)
  - [Configure Plugins](#configure-plugins)
  - [Complete Setup](#complete-setup)
- [Process Management](#-process-management)
  - [Find the Jenkins Process](#find-the-jenkins-process)
  - [Stop Jenkins](#stop-jenkins)
  - [Jenkins Data Location](#jenkins-data-location)
- [Job Management](#-job-management)
  - [Create a New Job](#create-a-new-job)
  - [Build a Job](#build-a-job)
  - [View Build Console Output](#view-build-console-output)
  - [Delete a Job](#delete-a-job)
- [Git Integration](#-git-integration)
  - [Install Git Plugin](#install-git-plugin)
  - [Get SSH Public Key from VPS](#get-ssh-public-key-from-vps)
  - [Add SSH Key to GitHub](#add-ssh-key-to-github)
  - [Add Credentials in Jenkins](#add-credentials-in-jenkins)
  - [Integrate Repository with Job](#integrate-repository-with-job)
- [Build Configuration](#-build-configuration)
  - [Add Build Steps](#add-build-steps)
  - [Add Multiple Build Steps](#add-multiple-build-steps)
- [Post-Build Actions](#-post-build-actions)
  - [Install Slack Notification Plugin](#install-slack-notification-plugin)
  - [Configure Slack Integration](#configure-slack-integration)
  - [Add Slack Credentials in Jenkins](#add-slack-credentials-in-jenkins)
  - [Configure Slack in System Settings](#configure-slack-in-system-settings)
  - [Add Slack Notification to Job](#add-slack-notification-to-job)
  - [Test the Integration](#test-the-integration)
- [Advanced Job Management](#-advanced-job-management)
  - [Copy an Existing Job](#copy-an-existing-job)
  - [Disable a Job](#disable-a-job)
  - [Enable a Job](#enable-a-job)
- [Triggers & Scheduling](#-triggers--scheduling)
  - [Build Periodically](#build-periodically)
  - [Poll SCM](#poll-scm)
  - [Trigger Builds Remotely](#trigger-builds-remotely)
  - [Build Periodically vs Poll SCM vs Remote Trigger](#build-periodically-vs-poll-scm-vs-remote-trigger)
- [Environment Variables](#-environment-variables)
  - [View Available Environment Variables](#view-available-environment-variables)
  - [Common Jenkins Environment Variables](#common-jenkins-environment-variables)
  - [Use Jenkins Environment Variables](#use-jenkins-environment-variables)
  - [Create Global Environment Variables](#create-global-environment-variables)
  - [Use Global Environment Variables](#use-global-environment-variables)
- [Build Parameters](#-build-parameters)
  - [Create a Parameterized Build](#create-a-parameterized-build)
  - [Use Parameters in Build Steps](#use-parameters-in-build-steps)
  - [Build with Parameters](#build-with-parameters)
  - [Parameter Types](#parameter-types)
- [Build History Management](#️-build-history-management)
  - [Discard Old Builds](#discard-old-builds)
- [Build Executors](#-build-executors)
  - [What Are Executors?](#what-are-executors)
  - [Configure Build Executors](#configure-build-executors)
  - [Executor Considerations](#executor-considerations)
- [User Management & Security](#-user-management--security)
  - [Create a New User](#create-a-new-user)
  - [Test the New User](#test-the-new-user)
  - [Allow Anonymous Read Access](#allow-anonymous-read-access)
  - [Install Role-Based Authorization Plugin](#install-role-based-authorization-plugin)
  - [Enable Role-Based Strategy](#enable-role-based-strategy)
  - [Create Roles](#create-roles)
  - [Assign Roles to Users](#assign-roles-to-users)
- [Custom Views](#-custom-views)
  - [Create a New View](#create-a-new-view)
  - [Configure List View](#configure-list-view)
  - [Access Custom Views](#access-custom-views)
  - [View Use Cases](#view-use-cases)
- [Distributed Builds](#️-distributed-builds)
  - [Install SSH Build Agents Plugin](#install-ssh-build-agents-plugin)
  - [Prepare the Node VPS](#prepare-the-node-vps)
  - [Create a New Node](#create-a-new-node)
  - [Configure the Node](#configure-the-node)
  - [Verify Connection](#verify-connection)
  - [Build Using a Node](#build-using-a-node)
  - [Benefits of Distributed Builds](#benefits-of-distributed-builds)
- [Monitoring](#-monitoring)
  - [View Build Trends](#view-build-trends)
  - [Build Statistics](#build-statistics)
- [Troubleshooting](#-troubleshooting)
- [Quick Reference](#-quick-reference)
- [Best Practices](#-best-practices)

---

## 🎯 Introduction

### Who Is This For?

- **Backend Programmer** — Automates build, test, and deploy for their own services
- **DevOps Engineer** — Sets up and maintains a self-hosted CI/CD server
- **System Administrator** — Manages Jenkins users, security, and build nodes on a VPS
- **Beginner in CI/CD** — Learns the core concepts of continuous integration hands-on

### What This Material Covers

- Installing Jenkins on a VPS from the ground up
- Configuring jobs, Git integration, build steps, and notifications
- Automating builds with triggers, environment variables, and parameters
- Securing Jenkins and scaling it with distributed build nodes

### Why Learn Jenkins?

- **Open source and free** — No license cost for self-hosting
- **Huge plugin ecosystem** — Integrates with almost every tool (Git, Slack, Docker, cloud providers)
- **Industry standard** — Still widely used for CI/CD in many companies
- **Full control** — Runs on your own infrastructure with your own rules

---

## 💻 System Requirements

> **Analogy:** Before opening a workshop, you need a room big enough for the machines. Jenkins can run in a small room, but the more projects (builds) you take on, the more space and power the workshop needs.

### Minimum Requirements

| Resource | Size |
| --- | --- |
| **RAM** | 256 MB |
| **Storage** | 1 GB |

### Recommended Requirements

| Resource | Size |
| --- | --- |
| **RAM** | 4 GB+ |
| **Storage** | 50 GB+ |

### Current Setup (Hostinger KVM 2)

| Resource | Size |
| --- | --- |
| **RAM** | 8 GB |
| **Storage** | 100 GB |

---

## 🖥️ VPS Setup

### Prepare the VPS

- **Purchase a VPS** — Choose a VPS that meets the minimum requirements from your preferred provider
- **Set the root password** — Create a password for VPS login through the provider's control panel
- **Get access credentials** — You will receive a **public IP address** and a **root username**

### Connect via SSH

```bash
ssh root@YOUR_PUBLIC_IP
```

Enter the password you created in the control panel.

### Generate SSH Key

```bash
ssh-keygen
```

Follow the prompts to complete SSH key generation.

> **Tip:** This SSH key is used later to connect Jenkins to GitHub. See [Git Integration](#-git-integration).

---

## 📦 Prerequisites Installation

### Install Git

```bash
sudo apt-get install git
```

Verify installation:

```bash
git --version
```

### Install Java 17

```bash
sudo apt install openjdk-17-jdk -y
```

Verify installation:

```bash
javac --version
```

> **Note:** Jenkins runs on Java. Make sure the Java version is compatible with the Jenkins version you download.

---

## 🚀 Jenkins Installation

### Download Jenkins

Download the Jenkins WAR file compatible with Java 17:

```bash
wget https://get.jenkins.io/war-stable/2.528.1/jenkins.war
```

### Start Jenkins

Run Jenkins on a specific port in the background:

```bash
java -jar jenkins.war --httpPort=PORT_NUMBER &
```

Example:

```bash
java -jar jenkins.war --httpPort=8080 &
```

### Access the Jenkins Web Interface

Get the VPS IP address:

```bash
hostname -I
```

Open your browser and navigate to:

```text
http://HOSTNAME:PORT_NUMBER
```

Example:

```text
http://192.168.1.100:8080
```

---

## 🔐 Initial Configuration

### Unlock Jenkins

Retrieve the initial admin password:

```bash
cat /root/.jenkins/secrets/initialAdminPassword
```

Copy the password and paste it into the Jenkins unlock page.

### Create Admin User

| Field | Value |
| --- | --- |
| **Username** | Your desired username |
| **Password** | Strong password |
| **Email** | Your email address |

### Configure Plugins

1. Select **"Select plugins to install"**
2. Click **"None"** to deselect all plugins
3. Click **"Next"**

> **Tip:** Start with no plugins and install only what you need later. Fewer plugins means a lighter, more secure Jenkins.

### Complete Setup

Click through the remaining setup screens to reach the Jenkins dashboard.

---

## 🛑 Process Management

### Find the Jenkins Process

```bash
ps aux | grep jenkins
```

### Stop Jenkins

| Command | Description |
| --- | --- |
| `kill -9 <PID>` | Kill a specific process by its PID |
| `pkill -f jenkins.war` | Kill all Jenkins processes |

### Jenkins Data Location

View the home directory contents:

```bash
ll
```

Navigate to the Jenkins directory:

```bash
cd .jenkins/
```

---

## 📋 Job Management

> **Analogy:** A job is like a recipe card in a kitchen. It describes exactly what to do — which ingredients (source code) to take and which steps to follow. Every time you "build," the kitchen cooks that recipe once, and the result is recorded in the order history (Build History).

### Create a New Job

1. Go to the Jenkins dashboard
2. Click **"New Item"** or **"Create a job"**
3. Enter the job name
4. Select the project type (e.g., **Freestyle project**)
5. Add a description (optional)
6. Click **"Save"**

### Build a Job

1. Navigate to the job detail page
2. Click **"Build Now"**
3. Wait for the build to complete
4. View the build in the **Build History** section

### View Build Console Output

1. Click on the build number in **Build History**
2. Select **"Console Output"**
3. Review the build logs

### Delete a Job

1. Go to the Jenkins dashboard
2. Click on the job to delete
3. Click **"Delete Project"**
4. Confirm by clicking **"Yes"**

---

## 🔌 Git Integration

> **Analogy:** Jenkins needs a key to enter the GitHub warehouse and pick up your source code. The SSH key pair works like a lock and key — the public key (lock) is installed on GitHub, and the private key (key) is handed to Jenkins.

### Install Git Plugin

1. Go to the Jenkins dashboard
2. Click the **settings icon** (Manage Jenkins)
3. Select **"Plugins"**
4. Click **"Available plugins"**
5. Search for **"Git Plugin"**
6. Check the installation box
7. Check **"Restart Jenkins when installation is complete and no jobs are running"**

### Get SSH Public Key from VPS

```bash
cd .ssh/
ll
cat id_rsa.pub
```

### Add SSH Key to GitHub

1. Copy the SSH key output
2. Go to GitHub → **Settings**
3. Select **"SSH and GPG keys"**
4. Click **"New SSH key"**
5. Enter a **Title**
6. Paste the SSH key
7. Click **"Add SSH key"**

### Add Credentials in Jenkins

1. Go to the Jenkins dashboard
2. Hover over **profile** → **"Credentials"**
3. Click **"Credentials"** in the left menu
4. Click on the **"Global credentials"** domain
5. Click **"Add Credentials"**
6. Fill in the form:

   | Field | Value |
   | --- | --- |
   | **Kind** | SSH Username with private key |
   | **ID** | Unique identifier |
   | **Description** | Credential description |
   | **Username** | Your GitHub username |
   | **Private Key** | Enter directly |

7. Click **"Add"** and paste the private key:

   ```bash
   cat ~/.ssh/id_rsa
   ```

8. Click **"Create"**

> **Gotcha:** The **public** key (`id_rsa.pub`) goes to GitHub, the **private** key (`id_rsa`) goes to Jenkins. Never share the private key anywhere else.

### Integrate Repository with Job

1. Go to the job detail page
2. Click **"Configure"**
3. Scroll to **"Source Code Management"**
4. Select **"Git"**
5. Enter the **Repository URL** (GitHub repo URL)
6. Select the **Credentials** from the dropdown
7. Specify the **Branch** (e.g., `*/main`)
8. Click **"Save"**

---

## 🔨 Build Configuration

> **Analogy:** Build steps are the instructions on an assembly line. Each station does one task in order — clean, install, test, build, ship. If one station fails, the line stops.

### Add Build Steps

1. Open the job detail page
2. Click **"Configure"**
3. Scroll to the **"Build Steps"** section
4. Click **"Add build step"**
5. Select **"Execute shell"**
6. Enter your shell commands

Example:

```bash
echo "Building application..."
npm install
npm run build
npm test
```

### Add Multiple Build Steps

Repeat the process above to add multiple build steps. Each step executes **sequentially**.

Common build step examples:

```bash
# Clean workspace
rm -rf dist/

# Install dependencies
npm install

# Run tests
npm test

# Build application
npm run build

# Deploy (example)
rsync -avz dist/ user@server:/var/www/html/
```

---

## 📢 Post-Build Actions

> **Analogy:** After the kitchen finishes an order, a bell rings so the waiter knows it's ready. Post-build actions are that bell — Jenkins tells your team (via Slack) whether the build succeeded or failed.

### Install Slack Notification Plugin

1. Go to the Jenkins dashboard
2. Click the **settings icon** (Manage Jenkins)
3. Select **"Plugins"**
4. Click **"Available plugins"**
5. Search for **"Slack Notification Plugin"**
6. Check the installation box
7. Check **"Restart Jenkins when installation is complete and no jobs are running"**

### Configure Slack Integration

1. Navigate to `https://my.slack.com/services/new/jenkins-ci`
2. Select or create a channel for Jenkins notifications
3. Enter the **Name** and **Description**
4. Click **"Create"**
5. Click **"Add Jenkins CI integration"**
6. Note the **Team Subdomain** and **Integration Token Credential ID**

### Add Slack Credentials in Jenkins

1. Go to the Jenkins dashboard
2. Hover over **profile** → **"Credentials"**
3. Click the **"Global credentials"** domain
4. Click **"Add Credentials"**
5. Fill in the form:

   | Field | Value |
   | --- | --- |
   | **Kind** | Secret text |
   | **Secret** | The Integration Token |
   | **ID** | Unique identifier |
   | **Description** | Credential description |

6. Click **"Create"**

### Configure Slack in System Settings

1. Click the **settings icon** (Manage Jenkins)
2. Select **"System"**
3. Scroll to the **"Slack"** section
4. Fill in the form:

   | Field | Value |
   | --- | --- |
   | **Workspace** | Team Subdomain |
   | **Credential** | The credential created earlier |
   | **Default channel / member id** | Slack channel name |

5. Click **"Save"**

### Add Slack Notification to Job

1. Go to the job detail page
2. Click **"Configure"**
3. Scroll to **"Post-build Actions"**
4. Click **"Add post-build action"**
5. Select **"Slack Notifications"**
6. Select notification preferences (build start, success, failure, etc.)
7. Click **"Save"**

### Test the Integration

Build the job to verify Slack notifications appear in the configured channel.

---

## 🔄 Advanced Job Management

### Copy an Existing Job

1. Go to the Jenkins dashboard
2. Click **"New Item"**
3. Enter the new job name
4. Select the project type
5. In the **"Copy from"** field, enter the job name to copy
6. Click **"OK"**
7. Modify the configuration as needed
8. Click **"Save"**

> **Tip:** Copying a job is the fastest way to create similar jobs (e.g., `app-staging` and `app-production`) without reconfiguring everything.

### Disable a Job

1. Go to the job detail page
2. Click **"Configure"**
3. Locate the toggle switch on the right side
4. Change from **"Enabled"** to **"Disabled"**
5. Click **"Save"**

When disabled, the **"Build Now"** button will not appear on the job page.

### Enable a Job

1. Go to the job detail page
2. Click **"Configure"**
3. Locate the toggle switch on the right side
4. Change from **"Disabled"** to **"Enabled"**
5. Click **"Save"**

---

## ⏰ Triggers & Scheduling

> **Analogy:** There are three ways to start the oven. Set a timer to start it every morning (Build Periodically). Peek into the fridge every minute and start cooking only when new ingredients arrive (Poll SCM). Or let someone call you and say "start now" (Trigger Remotely).

### Build Periodically

1. Go to the job detail page
2. Click **"Configure"**
3. Scroll to the **"Build Triggers"** section
4. Check **"Build periodically"**
5. Enter a schedule in cron format (use [crontab.guru](https://crontab.guru/))
6. Click **"Save"**

| Schedule | Cron Expression |
| --- | --- |
| Every hour | `0 * * * *` |
| Every day at midnight | `0 0 * * *` |
| Every Monday at 9 AM | `0 9 * * 1` |
| Every 15 minutes | `*/15 * * * *` |

### Poll SCM

1. Go to the job detail page
2. Click **"Configure"**
3. Scroll to the **"Build Triggers"** section
4. Check **"Poll SCM"**
5. Enter schedule: `* * * * *` (checks every minute)
6. Click **"Save"**

Jenkins checks the repository for changes and triggers builds **only when changes are detected**.

### Trigger Builds Remotely

1. Go to the job detail page
2. Click **"Configure"**
3. Scroll to the **"Build Triggers"** section
4. Check **"Trigger builds remotely"**
5. Enter an authentication token
6. Note the trigger URL format shown

Trigger URL format:

```text
http://JENKINS_URL/job/JOB_NAME/build?token=TOKEN_NAME
```

Test by navigating to the trigger URL in a browser.

### Build Periodically vs Poll SCM vs Remote Trigger

| Trigger | When It Builds | Best For |
| --- | --- | --- |
| **Build Periodically** | Always, on schedule | Nightly builds, scheduled reports |
| **Poll SCM** | On schedule, only if the repo changed | Building on new commits without webhooks |
| **Trigger Remotely** | When the URL is called | Integration with external scripts or tools |

---

## 🔧 Environment Variables

> **Analogy:** Environment variables are like labels stuck on a workbench — the job name, the build number, the workspace path. Any step can read the label instead of hard-coding the value.

### View Available Environment Variables

Access the environment variables reference at:

```text
http://JENKINS_URL/env-vars.html/
```

Example: `http://192.168.1.100:8080/env-vars.html/`

### Common Jenkins Environment Variables

| Variable | Description |
| --- | --- |
| `${JOB_NAME}` | Name of the job |
| `${BUILD_NUMBER}` | Current build number |
| `${BUILD_ID}` | Build ID |
| `${WORKSPACE}` | Job workspace directory |
| `${BUILD_URL}` | URL of the current build |

### Use Jenkins Environment Variables

1. Go to the job detail page
2. Click **"Configure"**
3. Scroll to **"Build Steps"**
4. Add an **"Execute shell"** step
5. Enter the command:

   ```bash
   echo "Executing Job ${JOB_NAME}:${BUILD_NUMBER}"
   ```

6. Click **"Save"**
7. Click **"Build Now"**
8. View **"Console Output"**

Example output:

```text
Executing Job Study Environment Variable:5
```

### Create Global Environment Variables

1. Go to the Jenkins dashboard
2. Click the **settings icon** (Manage Jenkins)
3. Select **"System"**
4. Scroll to the **"Global properties"** section
5. Check **"Environment variables"**
6. Click **"Add"**
7. Fill in the form:

   | Field | Example |
   | --- | --- |
   | **Name** | `AUTHOR` |
   | **Value** | `Dzaru Rizky Fathan Fortuna` |

8. Click **"Save"**

### Use Global Environment Variables

1. Go to the job detail page
2. Click **"Configure"**
3. Add an **"Execute shell"** build step
4. Enter the command:

   ```bash
   echo "Build with love by ${AUTHOR}"
   ```

5. Click **"Save"**
6. Build the job and check the console output

Example output:

```text
Build with love by Dzaru Rizky Fathan Fortuna
```

---

## 📊 Build Parameters

> **Analogy:** A parameterized build is like a coffee machine with buttons. The recipe stays the same, but before brewing you choose the size and the type. One job, many variations — chosen at build time.

### Create a Parameterized Build

1. Go to the job detail page
2. Click **"Configure"**
3. Check **"This project is parameterized"**
4. Click **"Add Parameter"**
5. Select the parameter type (e.g., **"Choice Parameter"**)
6. **Name:** Enter the parameter name (e.g., `ENVIRONMENT`)
7. **Choices:** Enter options (one per line):

   ```text
   development
   staging
   production
   ```

8. **Description:** Add a parameter description (optional)

### Use Parameters in Build Steps

1. Scroll to the **"Build Steps"** section
2. Add or modify an **"Execute shell"** step
3. Reference the parameter with `${PARAMETER_NAME}`:

   ```bash
   echo "Deploying to ${ENVIRONMENT}"
   ./deploy.sh ${ENVIRONMENT}
   ```

4. Click **"Save"**

### Build with Parameters

The **"Build Now"** button becomes **"Build with Parameters"**:

1. Click **"Build with Parameters"**
2. Select the parameter values
3. Click **"Build"**

### Parameter Types

| Type | Input |
| --- | --- |
| **String Parameter** | Free text input |
| **Choice Parameter** | Dropdown selection |
| **Boolean Parameter** | Checkbox (true/false) |
| **Password Parameter** | Masked text input |
| **File Parameter** | File upload |
| **Multi-line String Parameter** | Text area input |

---

## 🗄️ Build History Management

### Discard Old Builds

1. Go to the job detail page
2. Click **"Configure"**
3. Check **"Discard old builds"**
4. Select **"Strategy: Log Rotation"**
5. Fill in the form:

   | Field | Example |
   | --- | --- |
   | **Days to keep builds** | `7` |
   | **Max # of builds to keep** | `10` |

6. Click **"Save"**

Jenkins automatically removes builds that exceed **either** threshold when a new build completes.

> **Tip:** Enable this on every job. Old builds and their logs quietly fill up the VPS disk over time.

---

## ⚡ Build Executors

> **Analogy:** Executors are the number of cooks in the kitchen. Two cooks can prepare two orders at once. More cooks means more orders in parallel — but each cook needs space and gas (memory and CPU).

### What Are Executors?

Executors determine **how many builds can run simultaneously** on Jenkins.

### Configure Build Executors

1. Go to the Jenkins dashboard
2. Click the **settings icon** (Manage Jenkins)
3. Select **"System"**
4. Scroll to the **"# of executors"** field
5. Enter the desired number (e.g., `2`, `4`, `8`)
6. Click **"Save"**

### Executor Considerations

- **More executors** — More concurrent builds
- **Resource usage** — Each executor consumes memory and CPU
- **Sizing** — Set based on available system resources
- **Default** — Usually `2` for small setups

---

## 👥 User Management & Security

> **Analogy:** Jenkins is an office building. Users are employees with ID cards, anonymous read access is the public lobby where guests can look around, and roles are access levels — some cards open only the meeting rooms, others open the server room too.

### Create a New User

1. Go to the Jenkins dashboard
2. Click the **settings icon** (Manage Jenkins)
3. Select **"Users"**
4. Click **"Create User"**
5. Fill in the user details:

   | Field | Value |
   | --- | --- |
   | **Username** | Unique username |
   | **Password** | User password |
   | **Confirm password** | Confirm password |
   | **Full name** | User's full name |
   | **Email address** | User's email |

6. Click **"Create User"**

### Test the New User

1. Sign out of Jenkins
2. Log in with the new credentials
3. Verify access

### Allow Anonymous Read Access

1. Go to the Jenkins dashboard
2. Click the **settings icon** (Manage Jenkins)
3. Select **"Security"**
4. Scroll to the **"Authorization"** section
5. Select **"Logged-in users can do anything"**
6. Check **"Allow anonymous read access"**
7. Click **"Save"**

Guests can now view build lists and job details without logging in.

> **Gotcha:** Anonymous read access exposes job names, build logs, and console output to anyone who can reach the URL. Make sure no secrets are printed in the logs.

### Install Role-Based Authorization Plugin

1. Go to the Jenkins dashboard
2. Click the **settings icon** (Manage Jenkins)
3. Select **"Plugins"**
4. Click **"Available plugins"**
5. Search for **"Role-based Authorization Strategy"**
6. Check the installation box and restart

### Enable Role-Based Strategy

1. Click the **settings icon** (Manage Jenkins)
2. Select **"Security"**
3. Scroll to the **"Authorization"** section
4. Select **"Role-Based Strategy"**
5. Click **"Save"**

### Create Roles

1. Click the **settings icon** (Manage Jenkins)
2. Select **"Manage and Assign Roles"**
3. Click **"Manage Roles"**
4. In the **"Role to add"** field, enter the role name
5. Click **"Add"**
6. Configure permissions:

   | Permission Group | Scope |
   | --- | --- |
   | **Overall** | Global permissions |
   | **Job** | Job permissions (build, configure, read, etc.) |
   | **Run** | Execute permissions |
   | **View** | View permissions |
   | **SCM** | Source control permissions |

7. Click **"Save"**

### Assign Roles to Users

1. Go to **"Manage and Assign Roles"**
2. Click **"Assign Roles"**
3. Check the boxes to assign roles to users
4. Click **"Save"**

> **Example:** Users with the "User" role can view and build jobs but cannot access the **Manage Jenkins** menu.

---

## 📁 Custom Views

> **Analogy:** Custom views are like folders on a desk. All the documents (jobs) are still there, but you group them so each team only sees the pile that matters to them.

### Create a New View

1. Go to the Jenkins dashboard
2. Click the **"+"** icon (next to the "All" tab)
3. Enter the **View name**
4. Select the view type:

   | View Type | Description |
   | --- | --- |
   | **List View** | Custom list of selected jobs |
   | **My View** | Shows only jobs owned by the current user |

5. Click **"Create"**

### Configure List View

#### Job Filters

- In the **"Jobs"** section, select the jobs to include
- Check the boxes next to the desired jobs

#### Column Filters

- In the **"Columns"** section, add or remove columns:
  - Status
  - Weather (build stability)
  - Name
  - Last Success
  - Last Failure
  - Last Duration
  - Build Button
- Click **"OK"**

### Access Custom Views

Click the view tab on the Jenkins dashboard to display the filtered jobs.

### View Use Cases

| View | Purpose |
| --- | --- |
| **Team Views** | Group jobs by team or project |
| **Environment Views** | Separate dev, staging, production jobs |
| **Status Views** | Group by build status |
| **Priority Views** | Organize by importance |

---

## 🖥️ Distributed Builds

> **Analogy:** When the main kitchen gets too busy, the head chef (Jenkins master) opens branch kitchens (nodes). The head chef still takes every order and decides who cooks it, but the actual cooking can happen in any branch that has a free cook.

### Install SSH Build Agents Plugin

1. Go to the Jenkins dashboard
2. Click the **settings icon** (Manage Jenkins)
3. Select **"Plugins"**
4. Click **"Available plugins"**
5. Search for **"SSH Build Agents"**
6. Check the installation box and restart

### Prepare the Node VPS

- **Install Git** — `sudo apt-get install git`
- **Install Java 17** — `sudo apt install openjdk-17-jdk -y`
- **Match versions** — Use the same versions as the Jenkins master
- **Do NOT install Jenkins on the node** — The master connects to it and runs the agent automatically

### Create a New Node

1. Go to the Jenkins dashboard
2. Click the **settings icon** (Manage Jenkins)
3. Select **"Nodes"**
4. Click **"New Node"**
5. Enter the **Node name**
6. Select **"Permanent Agent"**
7. Click **"Create"**

### Configure the Node

1. **# of executors:** Number of concurrent builds (e.g., `2`)
2. **Remote root directory:** Working directory on the node — find it on the node VPS and copy the output path:

   ```bash
   ssh user@node-ip
   pwd
   ```

3. **Launch method:** Select **"Launch agents via SSH"**
4. **Host:** Enter the node VPS IP address
5. **Credentials:** Click **"Add"** → **"Jenkins"** and fill in the form:

   | Field | Value |
   | --- | --- |
   | **Kind** | Username with password |
   | **Username** | Node VPS username |
   | **Password** | Node VPS SSH password |
   | **ID** | Unique identifier |
   | **Description** | Credential description |

6. Click **"Add"**, then select the credential from the dropdown
7. Click **"Save"**

### Verify Connection

If successful, you'll see:

```text
Agent successfully connected and online
```

### Build Using a Node

- **First build may fail** — The node is still initializing
- **Subsequent builds should succeed**
- **Node executors are prioritized** — Jobs use available executors on nodes first
- **Fallback to master** — If node executors are full, builds run on the Jenkins master

### Benefits of Distributed Builds

- **Increased build capacity** — More executors across more machines
- **Parallel job execution** — Multiple heavy jobs at the same time
- **Resource distribution** — Load is spread instead of overloading the master
- **Isolated build environments** — Builds don't interfere with each other
- **Platform-specific builds** — Run builds on different operating systems

---

## 📈 Monitoring

### View Build Trends

1. Go to the job page
2. Locate the build item on the right side
3. Click **"Expand"**
4. View detailed build time trends with graphs

### Build Statistics

The build trend graph shows:

- **Build duration** over time
- **Success/failure rates**
- **Build frequency**
- **Performance trends**

---

## 🆘 Troubleshooting

| Problem | What to Check |
| --- | --- |
| **Jenkins won't start** | Port already in use · Java installation · System resources (RAM/CPU) |
| **Build fails with permission denied** | SSH credentials · File/directory permissions · Git configuration |
| **Plugin installation fails** | Internet connectivity · Jenkins version compatibility · Try manual plugin installation |
| **Node connection fails** | SSH credentials · Network connectivity · Java installed on node · Firewall rules |

---

## 🎯 Quick Reference

| Feature | Location | Purpose |
| --- | --- | --- |
| **Plugins** | Manage Jenkins → Plugins | Install Git, Slack, Role-based, SSH Build Agents |
| **Credentials** | Profile → Credentials → Global | Store SSH keys, tokens, and passwords |
| **Source Code Management** | Job → Configure | Connect a job to a Git repository |
| **Build Steps** | Job → Configure | Run shell commands sequentially |
| **Post-build Actions** | Job → Configure | Send Slack notifications after a build |
| **Build Triggers** | Job → Configure | Periodic, Poll SCM, or remote builds |
| **Global Properties** | Manage Jenkins → System | Define global environment variables |
| **Parameterized Build** | Job → Configure | Choose values at build time |
| **Discard Old Builds** | Job → Configure | Limit build history to save disk space |
| **# of Executors** | Manage Jenkins → System | Control concurrent builds |
| **Security** | Manage Jenkins → Security | Authorization strategy and anonymous access |
| **Manage and Assign Roles** | Manage Jenkins | Create and assign role-based permissions |
| **Nodes** | Manage Jenkins → Nodes | Add agents for distributed builds |

| Command | Purpose |
| --- | --- |
| `java -jar jenkins.war --httpPort=8080 &` | Start Jenkins in the background |
| `cat /root/.jenkins/secrets/initialAdminPassword` | Get the initial admin password |
| `ps aux \| grep jenkins` | Find the Jenkins process |
| `pkill -f jenkins.war` | Stop all Jenkins processes |
| `cat ~/.ssh/id_rsa.pub` | Show the SSH public key |

---

## 💡 Best Practices

**✅ Do This**

- **Use descriptive job names** — `backend-deploy`, `frontend-test`; use hyphens for multi-word names
- **Include the environment in the job name** — `app-staging`, `app-production`
- **Clean up old builds regularly** — enable [Discard Old Builds](#discard-old-builds) on every job
- **Monitor disk space and resource usage** on the VPS
- **Update plugins monthly and keep Jenkins updated**
- **Use role-based access control** — give each user only the permissions they need
- **Use strong passwords** for every account
- **Set an appropriate executor count** based on available RAM and CPU
- **Use distributed builds for heavy workloads** instead of overloading the master
- **Use parameterized builds** for flexibility instead of duplicating jobs
- **Use environment variables** for reusability across build steps
- **Implement proper error handling in scripts** and cache dependencies when possible

**❌ Avoid This**

- **Enabling anonymous read access carelessly** — build logs may leak sensitive information
- **Printing secrets in build steps** — store them as credentials instead
- **Installing every suggested plugin** — only install what you actually use
- **Installing Jenkins on build nodes** — nodes only need Git and Java
- **Running too many executors** on a small VPS — builds will compete for memory and slow down
- **Keeping unlimited build history** — it eventually fills the disk

> Reference: [Jenkins Documentation](https://www.jenkins.io/doc/) · [Jenkins Plugins Index](https://plugins.jenkins.io/) · [crontab.guru](https://crontab.guru/)
