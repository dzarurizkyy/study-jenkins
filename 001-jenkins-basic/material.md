# ⚙️ Jenkins Installation & Setup Guide

A comprehensive guide for installing and configuring Jenkins on a VPS.

---

## 📋 Table of Contents

- [System Requirements](#-system-requirements)
- [VPS Setup](#-vps-setup)
- [Prerequisites Installation](#-prerequisites-installation)
- [Jenkins Installation](#-jenkins-installation)
- [Initial Configuration](#-initial-configuration)
- [Process Management](#-process-management)
- [Job Management](#-job-management)
- [Git Integration](#-git-integration)
- [Build Configuration](#-build-configuration)
- [Post-Build Actions](#-post-build-actions)
- [Advanced Job Management](#-advanced-job-management)
- [Triggers & Scheduling](#-triggers--scheduling)
- [Environment Variables](#-environment-variables)
- [Build Parameters](#-build-parameters)
- [Build History Management](#-build-history-management)
- [Build Executors](#-build-executors)
- [User Management & Security](#-user-management--security)
- [Custom Views](#-custom-views)
- [Distributed Builds](#-distributed-builds)
- [Monitoring](#-monitoring)

---

## 💻 System Requirements

### Minimum Requirements
- **RAM:** 256 MB
- **Storage:** 1 GB

### Recommended Requirements
- **RAM:** 4 GB+
- **Storage:** 50 GB+

### Current Setup (Hostinger KVM 2)
- **RAM:** 8 GB
- **Storage:** 100 GB

---

## 🖥️ VPS Setup

- **Purchase VPS**
  
  Purchase a VPS that meets the minimum requirements from your preferred provider.

- **Set Root Password**
  
  Create a password for VPS login through the Hostinger control panel.

- **Get Access Credentials**
  
  You will receive:
  - Public IP address
  - Root username

- **Connect via SSH**
  
  ```
  ssh root@YOUR_PUBLIC_IP
  ```

- **Enter Password**
  
  Input the password you created in the control panel.

- **Generate SSH Key**
  
  ```
  ssh-keygen
  ```
  
  Follow the prompts to complete SSH key generation.

---

## 📦 Prerequisites Installation

- **Install Git**
  
  ```
  sudo apt-get install git
  ```
  
  Verify installation:
  
  ```
  git --version
  ```

- **Install Java 17**
  
  ```
  sudo apt install openjdk-17-jdk -y
  ```
  
  Verify installation:
  
  ```
  javac --version
  ```

---

## 🚀 Jenkins Installation

- **Download Jenkins**
  
  Download Jenkins WAR file compatible with Java 17:
  
  ```
  wget https://get.jenkins.io/war-stable/2.528.1/jenkins.war
  ```

- **Start Jenkins**
  
  Run Jenkins on a specific port in the background:
  
  ```
  java -jar jenkins.war --httpPort=PORT_NUMBER &
  ```
  
  Example:
  
  ```
  java -jar jenkins.war --httpPort=8080 &
  ```

- **Get VPS IP Address**
  
  ```
  hostname -I
  ```

- **Access Jenkins Web Interface**
  
  Open your browser and navigate to:
  
  ```
  http://HOSTNAME:PORT_NUMBER
  ```
  
  Example:
  
  ```
  http://192.168.1.100:8080
  ```

---

## 🔐 Initial Configuration

- **Unlock Jenkins**
  
  Retrieve the initial admin password:
  
  ```
  cat /root/.jenkins/secrets/initialAdminPassword
  ```
  
  Copy the password and paste it into the Jenkins unlock page.

- **Create Admin User**
  
  Set up your Jenkins admin account:
  - **Username:** Your desired username
  - **Password:** Strong password
  - **Email:** Your email address

- **Configure Plugins**
  
  When presented with plugin options:
  - Select **"Select plugins to install"**
  - Click **"None"** to deselect all plugins
  - Click **"Next"**
  
  You can install specific plugins later as needed.

- **Complete Setup**
  
  Click through the remaining setup screens to reach the Jenkins dashboard.

---

## 🛑 Process Management

- **Find Jenkins process**
  
  ```
  ps aux | grep jenkins
  ```

- **Kill specific process**
  
  ```
  kill -9 <PID>
  ```

- **Kill all Jenkins processes**
  
  ```
  pkill -f jenkins.war
  ```

- **View Jenkins data location**
  
  ```
  ll
  ```
  
  Navigate to Jenkins directory:
  
  ```
  cd .jenkins/
  ```

---

## 📋 Job Management

- **Create a new job**
  
  - Go to Jenkins dashboard
  - Click **"New Item"** or **"Create a job"**
  - Enter job name
  - Select project type (e.g., Freestyle project)
  - Add description (optional)
  - Click **"Save"**

- **Build a job**
  
  - Navigate to job detail page
  - Click **"Build Now"**
  - Wait for build completion
  - View build in **Build History** section

- **View build console output**
  
  - Click on build number in Build History
  - Select **"Console Output"**
  - Review build logs

- **Delete a job**
  
  - Go to Jenkins dashboard
  - Click on the job to delete
  - Click **"Delete Project"**
  - Confirm by clicking **"Yes"**

---

## 🔌 Git Integration

- **Install Git Plugin**
  
  - Go to Jenkins dashboard
  - Click **settings icon** (Manage Jenkins)
  - Select **"Plugins"**
  - Click **"Available plugins"**
  - Search for **"Git Plugin"**
  - Check the installation box
  - Check **"Restart Jenkins when installation is complete and no jobs are running"**

- **Get SSH public key from VPS**
  
  ```
  cd .ssh/
  ll
  cat id_rsa.pub
  ```

- **Add SSH key to GitHub**
  
  - Copy the SSH key output
  - Go to GitHub → **Settings**
  - Select **"SSH and GPG keys"**
  - Click **"New SSH key"**
  - Enter a **Title**
  - Paste the SSH key
  - Click **"Add SSH key"**

- **Add credentials in Jenkins**
  
  - Go to Jenkins dashboard
  - Hover over **profile** → **"Credentials"**
  - Click **"Credentials"** in left menu
  - Click on **"Global credentials"** domain
  - Click **"Add Credentials"**
  - **Kind:** Select **"SSH Username with private key"**
  - **ID:** Enter unique identifier
  - **Description:** Add description
  - **Username:** Your GitHub username
  - **Private Key:** Select **"Enter directly"**
  - Click **"Add"** and paste private key: <br />
  
  ```
  cat ~/.ssh/id_rsa
  ```
  
  - Click **"Create"**

- **Integrate repository with job**
  
  - Go to job detail page
  - Click **"Configure"**
  - Scroll to **"Source Code Management"**
  - Select **"Git"**
  - Enter **Repository URL** (GitHub repo URL)
  - Select **Credentials** from dropdown
  - Specify **Branch** (e.g., `*/main`)
  - Click **"Save"**

---

## 🔨 Build Configuration

- **Add build steps**
  
  - Open job detail page
  - Click **"Configure"**
  - Scroll to **"Build Steps"** section
  - Click **"Add build step"**
  - Select **"Execute shell"**
  - Enter your shell commands
  
  Example:
  
  ```
  echo "Building application..."
  npm install
  npm run build
  npm test
  ```

- **Add multiple build steps**
  
  Repeat the process above to add multiple build steps. Each step will execute sequentially.
  
  Common build step examples:
  
  ```
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

- **Install Slack Notification Plugin**
  
  - Go to Jenkins dashboard
  - Click **settings icon** (Manage Jenkins)
  - Select **"Plugins"**
  - Click **"Available plugins"**
  - Search for **"Slack Notification Plugin"**
  - Check the installation box
  - Check **"Restart Jenkins when installation is complete and no jobs are running"**

- **Configure Slack integration**
  
  - Navigate to `https://my.slack.com/services/new/jenkins-ci`
  - Select or create a channel for Jenkins notifications
  - Enter **Name** and **Description**
  - Click **"Create"**
  - Click **"Add Jenkins CI integration"**
  - Note the **Team Subdomain** and **Integration Token Credential ID**

- **Add Slack credentials in Jenkins**
  
  - Go to Jenkins dashboard
  - Hover over **profile** → **"Credentials"**
  - Click **"Global credentials"** domain
  - Click **"Add Credentials"**
  - **Kind:** Select **"Secret text"**
  - **Secret:** Paste the Integration Token
  - **ID:** Enter unique identifier
  - **Description:** Add description
  - Click **"Create"**

- **Configure Slack in system settings**
  
  - Click **settings icon** (Manage Jenkins)
  - Select **"System"**
  - Scroll to **"Slack"** section
  - **Workspace:** Enter Team Subdomain
  - **Credential:** Select the credential created earlier
  - **Default channel / member id:** Enter Slack channel name
  - Click **"Save"**

- **Add Slack notification to job**
  
  - Go to job detail page
  - Click **"Configure"**
  - Scroll to **"Post-build Actions"**
  - Click **"Add post-build action"**
  - Select **"Slack Notifications"**
  - Select notification preferences (build start, success, failure, etc.)
  - Click **"Save"**

- **Test the integration**
  
  Build the job to verify Slack notifications appear in the configured channel.

---

## 🔄 Advanced Job Management

- **Copy an existing job**
  
  - Go to Jenkins dashboard
  - Click **"New Item"**
  - Enter new job name
  - Select project type
  - In **"Copy from"** field, enter the job name to copy
  - Click **"OK"**
  - Modify configuration as needed
  - Click **"Save"**

- **Disable a job**
  
  - Go to job detail page
  - Click **"Configure"**
  - Locate toggle switch on the right side
  - Change from **"Enabled"** to **"Disabled"**
  - Click **"Save"**
  
  When disabled, the **"Build Now"** button will not appear on the job page.

- **Enable a job**
  
  - Go to job detail page
  - Click **"Configure"**
  - Locate toggle switch on the right side
  - Change from **"Disabled"** to **"Enabled"**
  - Click **"Save"**

---

## ⏰ Triggers & Scheduling

- **Schedule periodic builds**
  
  - Go to job detail page
  - Click **"Configure"**
  - Scroll to **"Build Triggers"** section
  - Check **"Build periodically"**
  - Enter schedule in cron format (use [crontab.guru](https://crontab.guru/))
  - Click **"Save"**
  
  Example schedules:
  
  ```
  # Every hour
  0 * * * *
  
  # Every day at midnight
  0 0 * * *
  
  # Every Monday at 9 AM
  0 9 * * 1
  
  # Every 15 minutes
  */15 * * * *
  ```

- **Poll SCM for changes**
  
  - Go to job detail page
  - Click **"Configure"**
  - Scroll to **"Build Triggers"** section
  - Check **"Poll SCM"**
  - Enter schedule: `* * * * *` (checks every minute)
  - Click **"Save"**
  
  Jenkins will check the repository for changes and trigger builds only when changes are detected.

- **Trigger builds remotely**
  
  - Go to job detail page
  - Click **"Configure"**
  - Scroll to **"Build Triggers"** section
  - Check **"Trigger builds remotely"**
  - Enter authentication token
  - Note the trigger URL format shown
  
  Trigger URL format:
  
  ```
  http://JENKINS_URL/job/JOB_NAME/build?token=TOKEN_NAME
  ```
  
  Test by navigating to the trigger URL in a browser.

---

## 🔧 Environment Variables

- **View available environment variables**
  
  Access the environment variables reference at:
  
  ```
  http://JENKINS_URL/env-vars.html/
  ```
  
  Example: `http://192.168.1.100:8080/env-vars.html/`

- **Use Jenkins environment variables**
  
  Common Jenkins environment variables:
  - `${JOB_NAME}` - Name of the job
  - `${BUILD_NUMBER}` - Current build number
  - `${BUILD_ID}` - Build ID
  - `${WORKSPACE}` - Job workspace directory
  - `${BUILD_URL}` - URL of the current build
  
  Example usage:
  
  - Go to job detail page
  - Click **"Configure"**
  - Scroll to **"Build Steps"**
  - Add **"Execute shell"** step
  - Enter command: <br />
  
  ```
  echo "Executing Job ${JOB_NAME}:${BUILD_NUMBER}"
  ```
  
  - Click **"Save"**
  - Click **"Build Now"**
  - View **"Console Output"**
  
  Example output: <br />
  
  ```
  Executing Job Study Environment Variable:5
  ```

- **Create global environment variables**
  
  - Go to Jenkins dashboard
  - Click **settings icon** (Manage Jenkins)
  - Select **"System"**
  - Scroll to **"Global properties"** section
  - Check **"Environment variables"**
  - Click **"Add"**
  - **Name:** Enter variable name (e.g., `AUTHOR`)
  - **Value:** Enter variable value (e.g., `Dzaru Rizky Fathan Fortuna`)
  - Click **"Save"**

- **Use global environment variables**
  
  - Go to job detail page
  - Click **"Configure"**
  - Add **"Execute shell"** build step
  - Enter command: <br />
  
    ```
    echo "Build with love by ${AUTHOR}"
    ```

  
  - Click **"Save"**
  - Build the job and check console output
  
  Example output:
  
  ```
  Build with love by Dzaru Rizky Fathan Fortuna
  ```

---

## 📊 Build Parameters

- **Create parameterized build**
  
  - Go to job detail page
  - Click **"Configure"**
  - Check **"This project is parameterized"**
  - Click **"Add Parameter"**
  - Select parameter type (e.g., **"Choice Parameter"**)
  - **Name:** Enter parameter name (e.g., `ENVIRONMENT`)
  - **Choices:** Enter options (one per line)
  
  Example choices:
  
  ```
  development
  staging
  production
  ```
  
  - **Description:** Add parameter description (optional)

- **Use parameters in build steps**
  
  - Scroll to **"Build Steps"** section
  - Add or modify **"Execute shell"** step
  - Reference parameter: `${PARAMETER_NAME}`
  
  Example:
  
  ```
  echo "Deploying to ${ENVIRONMENT}"
  ./deploy.sh ${ENVIRONMENT}
  ```
  
  - Click **"Save"**

- **Build with parameters**
  
  The **"Build Now"** button becomes **"Build with Parameters"**:
  
  - Click **"Build with Parameters"**
  - Select parameter values
  - Click **"Build"**

- **Parameter types**
  
  Available parameter types:
  - **String Parameter** - Free text input
  - **Choice Parameter** - Dropdown selection
  - **Boolean Parameter** - Checkbox (true/false)
  - **Password Parameter** - Masked text input
  - **File Parameter** - File upload
  - **Multi-line String Parameter** - Text area input

---

## 🗄️ Build History Management

- **Discard old builds**
  
  - Go to job detail page
  - Click **"Configure"**
  - Check **"Discard old builds"**
  - Select **"Strategy: Log Rotation"**
  - **Days to keep builds:** Number of days to retain (e.g., `7`)
  - **Max # of builds to keep:** Maximum number of builds (e.g., `10`)
  - Click **"Save"**
  
  Jenkins will automatically remove builds that exceed either threshold when a new build completes.

---

## ⚡ Build Executors

- **Configure build executors**
  
  - Go to Jenkins dashboard
  - Click **settings icon** (Manage Jenkins)
  - Select **"System"**
  - Scroll to **"# of executors"** field
  - Enter desired number (e.g., `2`, `4`, `8`)
  - Click **"Save"**
  
  **What are executors?**
  
  Executors determine how many builds can run simultaneously on Jenkins.
  
  **Considerations:**
  - More executors = More concurrent builds
  - Each executor consumes memory and CPU
  - Set based on available system resources
  - Default: Usually `2` for small setups

---

## 👥 User Management & Security

- **Create new user**
  
  - Go to Jenkins dashboard
  - Click **settings icon** (Manage Jenkins)
  - Select **"Users"**
  - Click **"Create User"**
  - Fill in user details:
    - **Username:** Unique username
    - **Password:** User password
    - **Confirm password:** Confirm password
    - **Full name:** User's full name
    - **Email address:** User's email
  - Click **"Create User"**

- **Test new user**
  
  - Sign out of Jenkins
  - Log in with new credentials
  - Verify access

- **Allow anonymous read access**
  
  - Go to Jenkins dashboard
  - Click **settings icon** (Manage Jenkins)
  - Select **"Security"**
  - Scroll to **"Authorization"** section
  - Select **"Logged-in users can do anything"**
  - Check **"Allow anonymous read access"**
  - Click **"Save"**
  
  Guests can now view build lists and job details without logging in.

- **Install Role-Based Authorization Plugin**
  
  - Go to Jenkins dashboard
  - Click **settings icon** (Manage Jenkins)
  - Select **"Plugins"**
  - Click **"Available plugins"**
  - Search for **"Role-based Authorization Strategy"**
  - Check installation box and restart

- **Enable Role-Based Strategy**
  
  - Click **settings icon** (Manage Jenkins)
  - Select **"Security"**
  - Scroll to **"Authorization"** section
  - Select **"Role-Based Strategy"**
  - Click **"Save"**

- **Create roles**
  
  - Click **settings icon** (Manage Jenkins)
  - Select **"Manage and Assign Roles"**
  - Click **"Manage Roles"**
  - In **"Role to add"** field, enter role name
  - Click **"Add"**
  - Configure permissions:
    - **Overall:** Global permissions
    - **Job:** Job permissions (build, configure, read, etc.)
    - **Run:** Execute permissions
    - **View:** View permissions
    - **SCM:** Source control permissions
  - Click **"Save"**

- **Assign roles to users**
  
  - Go to **"Manage and Assign Roles"**
  - Click **"Assign Roles"**
  - Check boxes to assign roles to users
  - Click **"Save"**
  
  Example: Users with "User" role can view and build jobs but cannot access **Manage Jenkins** menu.

---

## 📁 Custom Views

- **Create new view**
  
  - Go to Jenkins dashboard
  - Click **"+"** icon (next to "All" tab)
  - Enter **View name**
  - Select view type:
    - **List View** - Custom list of selected jobs
    - **My View** - Shows only jobs owned by current user
  - Click **"Create"**

- **Configure List View**
  
  **Job Filters:**
  
  - In **"Jobs"** section, select jobs to include
  - Check boxes next to desired jobs
  
  **Column Filters:**
  
  - In **"Columns"** section, add/remove columns:
    - Status
    - Weather (build stability)
    - Name
    - Last Success
    - Last Failure
    - Last Duration
    - Build Button
  - Click **"OK"**

- **Access custom views**
  
  Click the view tab on Jenkins dashboard to display filtered jobs.

- **View use cases**
  
  - **Team Views** - Group jobs by team or project
  - **Environment Views** - Separate dev, staging, production jobs
  - **Status Views** - Group by build status
  - **Priority Views** - Organize by importance

---

## 🖥️ Distributed Builds

- **Install SSH Build Agents Plugin**
  
  - Go to Jenkins dashboard
  - Click **settings icon** (Manage Jenkins)
  - Select **"Plugins"**
  - Click **"Available plugins"**
  - Search for **"SSH Build Agents"**
  - Check installation box and restart

- **Prepare node VPS**
  
  **Requirements:**
  - Install Git: `sudo apt-get install git`
  - Install Java 17: `sudo apt install openjdk-17-jdk -y`
  - Use same versions as Jenkins master
  - **Do NOT install Jenkins on the node**

- **Create new node**
  
  - Go to Jenkins dashboard
  - Click **settings icon** (Manage Jenkins)
  - Select **"Nodes"**
  - Click **"New Node"**
  - Enter **Node name**
  - Select **"Permanent Agent"**
  - Click **"Create"**

- **Configure node**
  
  - **# of executors:** Number of concurrent builds (e.g., `2`)
  - **Remote root directory:** Working directory on node
    
    Find directory on node VPS:
    
    ```
    ssh user@node-ip
    pwd
    ```
    
    Copy the output path
  
  - **Launch method:** Select **"Launch agents via SSH"**
  - **Host:** Enter node VPS IP address
  - **Credentials:** Click **"Add"** → **"Jenkins"**
    - **Kind:** Select **"Username with password"**
    - **Username:** Node VPS username
    - **Password:** Node VPS SSH password
    - **ID:** Enter unique identifier
    - **Description:** Add description
    - Click **"Add"**
  - Select the credential from dropdown
  - Click **"Save"**

- **Verify connection**
  
  If successful, you'll see:
  
  ```
  Agent successfully connected and online
  ```

- **Build using node**
  
  - First build may fail (initialization)
  - Subsequent builds should succeed
  - Jobs prioritize available executors on nodes
  - If node executors are full, builds use Jenkins master

- **Benefits of distributed builds**
  
  - Increased build capacity
  - Parallel job execution
  - Resource distribution
  - Isolated build environments
  - Platform-specific builds (different OS)

---

## 📊 Monitoring

- **View build trends**
  
  - Go to job page
  - Locate build item on the right side
  - Click **"Expand"** item
  - View detailed build time trends with graphs

- **Build statistics**
  
  The build trend graph shows:
  - Build duration over time
  - Success/failure rates
  - Build frequency
  - Performance trends

---

## 💡 Tips & Best Practices

- **Job Naming**
  - Use descriptive names: `backend-deploy`, `frontend-test`
  - Use hyphens for multi-word names
  - Include environment: `app-staging`, `app-production`

- **Regular Maintenance**
  - Clean up old builds regularly
  - Monitor disk space usage
  - Update plugins monthly
  - Review security settings

- **Security**
  - Use role-based access control
  - Enable anonymous read access carefully
  - Use strong passwords
  - Keep Jenkins updated

- **Performance**
  - Set appropriate executor count
  - Discard old builds automatically
  - Use distributed builds for heavy workloads
  - Monitor resource usage

- **Build Efficiency**
  - Use parameterized builds for flexibility
  - Implement proper error handling in scripts
  - Use environment variables for reusability
  - Cache dependencies when possible

---

## 🆘 Troubleshooting

- **Jenkins won't start**
  - Check if port is already in use
  - Verify Java installation
  - Check system resources (RAM/CPU)

- **Build fails with permission denied**
  - Verify SSH credentials
  - Check file/directory permissions
  - Ensure proper Git configuration

- **Plugin installation fails**
  - Check internet connectivity
  - Verify Jenkins version compatibility
  - Try manual plugin installation

- **Node connection fails**
  - Verify SSH credentials
  - Check network connectivity
  - Ensure Java is installed on node
  - Verify firewall rules