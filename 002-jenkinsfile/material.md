# ⚙️ Jenkins Pipeline Guide

A comprehensive guide for implementing Continuous Delivery pipelines in Jenkins using Groovy DSL.

---

## 📋 Table of Contents

- [Overview](#-overview)
- [Installation](#-installation)
- [Core Concepts](#-core-concepts)
- [Creating Pipeline Jobs](#-creating-pipeline-jobs)
- [Pipeline with SCM](#-pipeline-with-scm)
- [Agent Configuration](#-agent-configuration)
- [Post Actions](#-post-actions)
- [Stages](#-stages)
- [Steps](#-steps)
- [Scripts](#-scripts)
- [Utility Steps](#-utility-steps)
- [Agent Per Stage](#-agent-per-stage)
- [Global Variables](#-global-variables)
- [Environment Variables](#-environment-variables)
- [Credentials](#-credentials)
- [Options](#-options)
- [Parameters](#-parameters)
- [Triggers](#-triggers)
- [Input](#-input)
- [When Conditions](#-when-conditions)
- [Sequential Stages](#-sequential-stages)
- [Parallel Execution](#-parallel-execution)
- [Matrix Builds](#-matrix-builds)
- [Credential Binding](#-credential-binding)
- [Multi-Branch Pipeline](#-multi-branch-pipeline)

---

## 🎯 Overview

**Jenkins Pipeline** is a plugin that supports implementation of continuous delivery pipelines in Jenkins.

### What is a Continuous Delivery Pipeline?

A continuous delivery pipeline consists of commands created to deliver software to users, from version control through deployment.

### Why Use Pipeline?

- Since pipelines are created using code, it's easy to modify or review pipeline stages
- Pipelines are usually created in files and stored in the project, so there's no fear of data loss during Jenkins restart or failure

---

## 📦 Installation

Jenkins Pipeline is **not included** by default when installing Jenkins.

- Go to Jenkins dashboard
- Click **settings icon** (Manage Jenkins)
- Select **"Plugins"**
- Click **"Available plugins"**
- Search for **"Pipeline"**
- Install **Pipeline: Aggregator** plugin
- Check **"Restart Jenkins when installation is complete and no jobs are running"**

---

## 🧩 Core Concepts

  - **Pipeline**
    
    The complete definition of your continuous delivery code. A pipeline contains all code instructions for the continuous delivery process, such as compile, testing, deploy, etc.
  
  - **Agent**
    
    A machine or server that is part of Jenkins infrastructure used to execute the pipeline.
  
  - **Stage**
    
    A block that defines a task or phase in the pipeline (e.g., "Build", "Test", "Deploy"). Stages are typically displayed in Jenkins as progress indicators. Usually, a pipeline contains many stages.
  
  - **Step**
    
    An individual instruction or command. Steps are essentially instructions on what Jenkins should do. Steps are performed within stages.

---

## 🚀 Creating Pipeline Jobs

- **Create basic pipeline job**

  - Go to Jenkins dashboard
  - Click **"New Item"**
  - Enter job name
  - Select **"Pipeline"** as item type
  - Click **"OK"**
  - Scroll to **"Pipeline"** section
  - Select **"Pipeline script"** in Definition dropdown

- **Simple pipeline example**

  ```groovy
  pipeline {
    agent any
    stages {
      stage("Hello") {
        steps {
          echo "Hello World"
        }
      }
    }
  }
  ```

---

## 🔗 Pipeline with SCM

- **Setup repository**

  - Select your project to integrate with Jenkins
  - Add `Jenkinsfile` to project root
  - Add pipeline code to Jenkinsfile
  - Commit and push to repository:

    ```bash
    git init
    git add .
    git commit -m "commit message"
    git remote add origin GITHUB_URL
    git push -u origin BRANCH_NAME
    ```

- **Create pipeline job from SCM**

  - Go to Jenkins dashboard
  - Click **"New Item"**
  - Enter job name
  - Select **"Pipeline"** as item type
  - Click **"OK"**
  - Scroll to **"Pipeline"** section
  - Select **"Pipeline script from SCM"** in Definition dropdown
  - **SCM:** Select **"Git"**
  - **Repository URL:** Enter your GitHub repository URL
  - **Credentials:** Select your GitHub credentials
  - **Branch Specifier:** Enter branch name (e.g., `*/main`)
  - **Script Path:** Enter Jenkinsfile path (default: `Jenkinsfile`)
  - Click **"Save"**
  - Click **"Build Now"**

---

## 🖥️ Agent Configuration

The agent section determines where the pipeline executes.

- **Agent Types**

  | Type | Description |
  |------|-------------|
  | `any` | Pipeline will be executed on any agent, including master |
  | `none` | Pipeline will not be executed on any agent, meaning it runs on master |
  | `label` | Pipeline will only run on agents with specified labels |
  | `node` | Similar to label, but allows custom workspace location |
  | `docker` | Pipeline runs in a Docker container |
  | `dockerfile` | Like docker, but container image is built from Dockerfile |
  | `kubernetes` | Pipeline runs in Kubernetes cluster |

- **Basic agent configuration**

  ```groovy
  pipeline {
    agent any
    stages {
      stage("Build") {
        steps {
          echo "Building..."
        }
      }
    }
  }
  ```

- **Agent with node label**

  Sometimes you want to run a pipeline only on specific agents. For example, if you have Jenkins agents with Fedora or Windows operating systems, and you want the pipeline to run on those agents, you can change the agent value to the desired label or node.

  ```groovy
  pipeline {
    agent {
      node {
        label "linux && java11"
      }
    }
    stages {
      stage("Build") {
        steps {
          echo "Building on Linux with Java 11"
        }
      }
    }
  }
  ```

- **Add labels to Jenkins agents**

  - Go to Jenkins dashboard
  - Select **"Build Executor Status"**
  - Click on the node you want to label
  - Click **"Configure"**
  - In **"Labels"** field, enter labels (space-separated for multiple)
  - Click **"Save"**

  Example labels: `linux java11 docker`

---

## 📢 Post Actions

Post is a section used to add steps at the end when a pipeline condition is met. For example, after the pipeline is complete, you want to send a message to Slack or email. Or if an error occurs in the pipeline, you want to send a notification to Slack.

- **Available Post Conditions**

  | Condition | Description |
  |-----------|-------------|
  | `always` | Always executes regardless of any condition |
  | `changed` | Executes if pipeline status changed from previous build |
  | `fixed` | Executes if pipeline status changed from previous error to success |
  | `regression` | Executes if pipeline status changed from previous success to failure |
  | `aborted` | Executes if pipeline was manually aborted |
  | `failure` | Executes if pipeline status is error |
  | `success` | Executes if pipeline status is success |
  | `cleanup` | Always executes but after all other post conditions |

- **Post configuration example**

  ```groovy
  pipeline {
    agent any
    stages {
      stage("Build") {
        steps {
          echo "Building..."
        }
      }
    }
    post {
      always {
        echo "I will always say Hello again!"
      }
      success {
        echo "Yay, success"
      }
      failure {
        echo "Oh no, failure"
      }
      cleanup {
        echo "Don't care success or error"
      }
    }
  }
  ```

---

## 📊 Stages

Stages is a section where there is one or more stage blocks. Stages usually contain details of the phases in the continuous delivery pipeline.

- **Key Points**
  - Stages execute **sequentially** in order
  - If a stage has an error, subsequent stages won't execute
  - No naming restrictions - name stages as you prefer
  - Common stage names: Build, Test, Deploy

- **Multiple Stages Example**

  ```groovy
  pipeline {
    agent any
    stages {
      stage("Build") {
        steps {
          echo "Build"
        }
      }
      stage("Test") {
        steps {
          echo "Test"
        }
      }
      stage("Deploy") {
        steps {
          echo "Deploy"
        }
      }
    }
  }
  ```

- **Install Pipeline Stage View plugin**

  Sometimes you want to see the build process at each stage visually.

  **Plugin URL:** https://plugins.jenkins.io/pipeline-stage-view/

  - Install plugin from Jenkins Plugin Manager
  - View stage visualization on job page

---

## 🔧 Steps

Steps contain the instructions you want to execute in the pipeline.

- **Basic Steps Plugin**

  When installing the pipeline plugin, it automatically installs the pipeline basic steps plugin, which contains commands or instructions that can be used.

  - **Documentation:** https://www.jenkins.io/doc/pipeline/steps/workflow-basic-steps/

  - **Basic Steps Example**
  
    ```groovy
    pipeline {
      agent any
      stages {
        stage("Build") {
          steps {
            echo("Build")
            sleep(10)
            echo("Finish Build")
          }
        }
      }
    }
    ```

- **Node and Process Steps**

  One of the most commonly used steps is node and process steps. This plugin is typically used to run or execute terminal commands, such as shell scripts (Unix) or command scripts (Windows).

  - **Documentation:** https://www.jenkins.io/doc/pipeline/steps/workflow-durable-task-step/#sh-shell-script

  - **Shell Script Example**
  
    ```groovy
    pipeline {
      agent any
      stages {
        stage("Test") {
          steps {
            sh("cat note.txt")
          }
        }
      }
    }
    ```

---

## 💻 Scripts

Sometimes you need to create a very flexible pipeline. Pipeline supports scripts where you can insert Groovy code into the pipeline.

- **Important Notes**

  - By default, using Groovy code inside steps will cause an error
  - You need to add a `script` tag to mark that this section contains Groovy code

- **Script Example**

    ```groovy
    pipeline {
      agent any
      stages {
        stage("Build") {
          steps {
            echo("Build")
            sleep(10)
            
            script {
              for (int i = 0; i < 10; i++) {
                echo("Script ${i}")
              }
            }
  
            echo("Finish Build")
          }
        }
      }
    }
    ```

---

## 🛠️ Utility Steps

A plugin containing utilities that can be used to simplify pipeline creation. Examples include reading files, creating archive files, creating hashes, etc.

- **Documentation:** https://www.jenkins.io/doc/pipeline/steps/pipeline-utility-steps/

- **Utility Steps Example**

  ```groovy
  pipeline {
    agent any
    stages {
      stage("Test") {
        steps {
          sh("cat note.txt")

          script {
            def data = [
              "firstName": "Dzaru",
              "lastName": "Rizky Fathan Fortuna"
            ]
            writeJSON(file: "data.json", json: data)
          }
        }
      }
    }
  }
  ```

---

## 🎯 Agent Per Stage

When using an agent, all stages will automatically run on that agent. But sometimes you want to run stages on different agents.

- **Use Case**

  For example, the first stage needs a Java agent, the second stage needs a Golang agent, etc. In this case, you can set the agent in the pipeline to `none`, then add an agent to each stage.

- **Agent per Stage Example**

  ```groovy
  pipeline {
    agent none
    stages {
      stage("Build") {
        agent {
          node {
            label "linux && java17"
          }
        }
        steps {
          echo("Build")
          sleep(10)
          
          script {
            for (int i = 0; i < 10; i++) {
              echo("Script ${i}")
            }
          }

          echo("Finish Build")
        }
      }
      stage("Test") {
        agent {
          node {
            label "linux && golang"
          }
        }
        steps {
          echo("Test")
        }
      }
    }
  }
  ```

---

## 🌍 Global Variables

You can get global variables in Jenkins not only manually, but also using pipelines. Since pipelines use Groovy code, you can access information using global variables that are automatically accessible in Groovy.

- **Documentation**: JENKINS_URL/job/JOB_NAME/pipeline-syntax/globals

- **Common global variables**

  - **env** - Used to get global information from Jenkins
  - **currentBuild** - Used to get information about the currently running build job
  
- **Global Variable Example**

  ```groovy
  pipeline {
    agent any
    stages {
      stage("Info") {
        steps {
          echo("Job Name: ${env.JOB_NAME}")
          echo("Build Number: ${env.BUILD_NUMBER}")
          echo("Build Status: ${currentBuild.currentResult}")
        }
      }
    }
  }
  ```

---

## 🔐 Environment Variables

Pipelines also support adding environment variables.

- **Environment Variable Scope**

  - If an environment variable is added to the pipeline, all stages can access its value
  - If an environment variable is added to a stage, it can only be accessed within that stage

- **Environment Variable Example**

  ```groovy
  pipeline {
    agent any
    environment {
      AUTHOR = "Dzaru Rizky"
      EMAIL = "dzaru@example.com"
    }
    stages {
      stage("Build") {
        environment {
          APP = "My Application"
        }
        steps {
          echo("Author: ${AUTHOR}")
          echo("Email: ${EMAIL}")
          echo("App: ${APP}")
        }
      }
      stage("Test") {
        steps {
          echo("Author: ${AUTHOR}")
          echo("Email: ${EMAIL}")
          // APP variable not accessible here
        }
      }
    }
  }
  ```

---

## 🔑 Credentials

In the environment section, there's a special command called `credentials()` that can be used to retrieve data from Jenkins credentials. This is more secure than having to type it manually in the Jenkinsfile.

### Supported Credential Types

| Type | Behavior |
|------|----------|
| **Secret text** | Environment variable contains the secret text value |
| **Secret file** | Environment variable contains the location of a temporary secret file (automatically deleted after pipeline completes) |
| **Username and password** | Environment variable contains `username:password`, and automatically creates `VARNAME_USR` and `VARNAME_PSW` variables |
| **SSH with private key** | Environment variable contains the location of a temporary SSH file. Also automatically creates `VARNAME_USR` and `VARNAME_PSW` (containing SSH passphrase) |

- **Credentials example**

  ```groovy
  pipeline {
    agent any
    environment {
      APP = credentials("credential-test")
    }
    stages {
      stage("Build") {
        steps {
          echo("App User: ${APP_USR}")
          sh('echo "App Password: $APP_PSW" > "secret.txt"')
        }
      }
    }
  }
  ```

### Sensitive Information Handling

The `${key}` syntax inside double quotes is Groovy String Interpolation and should not be used for sensitive data like credentials. To prevent sensitive information from being visible, use single quotes `''` and `$Key`.

**Correct way for sensitive data:**

```groovy
sh('echo "Password: $APP_PSW" > "secret.txt"')
```

**Incorrect way (exposes data):**

```groovy
sh("echo 'Password: ${APP_PSW}' > 'secret.txt'")
```

---

## ⚙️ Options

Pipeline has an options command used for pipeline settings. Options can be at the pipeline level or stage level (like environment).

**Documentation:** https://www.jenkins.io/doc/book/pipeline/syntax/#options

### Common Options

- `disableConcurrentBuilds()` - Prevents concurrent builds
- `timeout(time: 10, unit: 'MINUTES')` - Sets pipeline timeout
- `retry(3)` - Retries the stage on failure
- `timestamps()` - Adds timestamps to console output

- **Options example**

  ```groovy
  pipeline {
    agent any
    options {
      disableConcurrentBuilds()
      timeout(time: 10, unit: 'MINUTES')
    }
    stages {
      stage("Build") {
        steps {
          echo "Building..."
        }
      }
    }
  }
  ```

---

## 📝 Parameters

In pipelines, you can also add parameters using the `parameters` command. Parameters input by users will automatically be stored in global variables.

### Parameter Types

| Type | Description |
|------|-------------|
| `string` | Text/string parameter type |
| `text` | Similar to string but input is multiline text area |
| `booleanParam` | Boolean parameter type (true/false) |
| `choice` | String/text parameter with predefined options |
| `password` | String parameter considered sensitive |

- **Parameters example**

  ```groovy
  pipeline {
    agent any
    parameters {
      string(name: "NAME", defaultValue: "Guest", description: "What is your name?")
      text(name: "DESCRIPTION", defaultValue: "", description: "Tell me about you?")
      booleanParam(name: "DEPLOY", defaultValue: false, description: "Need to deploy?")
      choice(name: "SOCIAL_MEDIA", choices: ["Instagram", "Facebook", "Twitter"], description: "Which Social Media?")
      password(name: "SECRET", defaultValue: "", description: "Encrypt Key")
    }
    stages {
      stage("Build") {
        steps {
          echo("Hello ${params.NAME}")
          echo("Description: ${params.DESCRIPTION}")
          echo("Deploy: ${params.DEPLOY}")
          echo("Social Media: ${params.SOCIAL_MEDIA}")
        }
      }
    }
  }
  ```

---

## ⏰ Triggers

A command used to run jobs automatically.

### Trigger Types

| Type | Description |
|------|-------------|
| `cron` | Runs job automatically based on cron expression |
| `pollSCM` | Uses cron expression to automatically check for SCM changes. If changes occur, job runs automatically |
| `upstream` | Runs job after another job completes with a specific result |

- **Trigger example**

  ```groovy
  pipeline {
    agent any
    triggers {
      pollSCM("* * * * *")
      // cron("0 0 * * *")
    }
    stages {
      stage("Build") {
        steps {
          echo "Building..."
        }
      }
    }
  }
  ```

---

## 💬 Input

Input is similar to parameters. Input is a command added to a stage. When input is added to a stage, the stage won't run until the input is filled by the user.

### Input Options

| Option | Description |
|--------|-------------|
| `id` | Input identifier, defaults to stage name |
| `message` | Message displayed to user |
| `ok` | Text for OK button |
| `submitter` | Users allowed to input (comma-separated for multiple) |
| `parameters` | Parameters that need to be input by user |

- **Input example**

  ```groovy
  pipeline {
    agent any
    stages {
      stage("Deploy") {
        input {
          message "Can we deploy?"
          ok "Yes, of course"
          submitter "dzarurizky"
          parameters {
            choice(name: "TARGET_ENV", choices: ["DEV", "QA", "PROD"], description: "Which Environment?")
          }
        }
        steps {
          echo("Deploy to ${TARGET_ENV}")
        }
      }
    }
  }
  ```

---

## ❓ When Conditions

A command used to determine under what conditions a stage is executed.

**Documentation:** https://www.jenkins.io/doc/book/pipeline/syntax/#when

### Common When Conditions

- `branch` - Execute when on specific branch
- `expression` - Execute when expression returns true
- `environment` - Execute when environment variable matches value
- `equals` - Execute when values are equal

- **When example**

  ```groovy
  pipeline {
    agent any
    parameters {
      booleanParam(name: "DEPLOY", defaultValue: false, description: "Deploy?")
    }
    stages {
      stage("Build") {
        steps {
          echo "Building..."
        }
      }
      stage("Release") {
        when {
          expression {
            return params.DEPLOY
          }
        }
        steps {
          echo "Release it"
        }
      }
    }
  }
  ```

---

## 📚 Sequential Stages

Stages can have stages within them. Stages inside execute sequentially by default.

### Important Notes

- Stages can only have one command: `steps`, `stages`, `parallel`, or `matrix`
- If you add stages again, you can't combine it with steps

- **Sequential stages example**

  ```groovy
  pipeline {
    agent none
    stages {
      stage("Prepare") {
        agent {
          node {
            label "linux && java17"
          }
        }
        stages {
          stage("Global Variable") {
            steps {
              echo("Author: ${env.AUTHOR}")
              echo("Email: ${env.EMAIL}")
            }
          }
          stage("Environment Variable") {
            steps {
              echo("Start Job: ${env.JOB_NAME}")
              echo("Start Build: ${env.BUILD_NUMBER}")
              echo("Branch Name: ${env.BRANCH_NAME}")
            }
          }
        }
      }
    }
  }
  ```

---

## ⚡ Parallel Execution

In certain cases, you want stages to run in parallel.

### Key Points

- By default, parallel waits for all processes to complete, even if one stage has an error
- To automatically stop all stage processes when one stage has an error, add `parallelAlwaysFailFast()` to options
- When using parallel, you can't add an agent to the parent stage, so you need to specify it in each parallel stage

- **Parallel example**

  ```groovy
  pipeline {
    agent none
    options {
      parallelAlwaysFailFast()
    }
    stages {
      stage("Prepare") {
        parallel {
          stage("Global Variable") {
            agent {
              node {
                label "linux && java17"
              }
            }
            steps {
              echo("Author: ${env.AUTHOR}")
              echo("Email: ${env.EMAIL}")
            }
          }
          stage("Environment Variable") {
            agent {
              node {
                label "linux && java17"
              }
            }
            steps {
              echo("Start Job: ${env.JOB_NAME}")
              echo("Start Build: ${env.BUILD_NUMBER}")
              echo("Branch Name: ${env.BRANCH_NAME}")
            }
          }
        }
      }
    }
  }
  ```

---

## 🔢 Matrix Builds

Matrix is a feature that can be used to define a multi-dimensional matrix containing name-value combinations, executed in parallel.

### Key Points

- Matrix is very powerful because it can run stages in parallel with predetermined matrix combinations
- Since matrix runs in parallel, like parallel, you can also use the `failFast` or `parallelAlwaysFailFast()` option

### Matrix Cell

When creating axes in a matrix, stages will automatically be built using combinations of matrix axis values, called matrix cells.

For example, with two axes (OS and ARC), you'll get several combinations:
- linux 32, linux 64
- windows 32, windows 64
- mac 32, mac 64

You can automatically retrieve axis data from environment variables in the stage.

- **Matrix example**

  ```groovy
  pipeline {
    agent none
    stages {
      stage("Matrix") {
        matrix {
          axes {
            axis {
              name "OS"
              values "linux", "windows", "mac"
            }
            axis {
              name "ARC"
              values "32", "64"
            }
          }
          stages {
            stage("OS Setup") {
              agent {
                node {
                  label "linux && java17"
                }
              }
              steps {
                echo("Setup ${OS} ${ARC}")
              }
            }
          }
        }
      }
    }
  }
  ```

- **Exclude matrix cell**

  Matrix also has an `exclude` command if you want to exclude certain cells. For example, you want to exclude mac 32 because there's no longer a mac 32 version.

  ```groovy
  pipeline {
    agent none
    stages {
      stage("Matrix") {
        matrix {
          axes {
            axis {
              name "OS"
              values "linux", "windows", "mac"
            }
            axis {
              name "ARC"
              values "32", "64"
            }
          }
          excludes {
            exclude {
              axis {
                name "OS"
                values "mac"
              }
              axis {
                name "ARC"
                values "32"
              }
            }
          }
          stages {
            stage("OS Setup") {
              agent {
                node {
                  label "linux && java17"
                }
              }
              steps {
                echo("Setup ${OS} ${ARC}")
              }
            }
          }
        }
      }
    }
  }
  ```

---

## 🔐 Credential Binding

Previously, we used the `credentials()` command to retrieve data from Jenkins credentials securely. But sometimes you only want to use credentials in a specific section and don't want to expose them to environment variables.

You can use the Credentials Binding plugin.

**Plugin URL:** https://plugins.jenkins.io/credentials-binding/

**Documentation:** https://www.jenkins.io/doc/pipeline/steps/credentials-binding/

- **Credential binding example**

  ```groovy
  pipeline {
    agent any
    stages {
      stage("Deploy") {
        input {
          message "Can we deploy?"
          ok "Yes, of course"
          submitter "dzarurizky"
          parameters {
            choice(name: "TARGET_ENV", choices: ["DEV", "QA", "PROD"], description: "Which Environment?")
          }
        }
        steps {
          withCredentials([usernamePassword(
            credentialsId: "credential-test",
            usernameVariable: "USER",
            passwordVariable: "PASSWORD"
          )]) {
            echo("Deploy to ${TARGET_ENV}")
            sh('echo "Release it with -u $USER -p $PASSWORD" > "release.txt"')
          }
        }
      }
    }
  }
  ```

---

## 🌿 Multi-Branch Pipeline

Previously, we only created jobs from a Git repository with a predetermined branch. In reality, using only one branch isn't very useful - you want the job process to automatically run on all branches in the Git repository.

Jenkins Pipeline has a multi-branch pipeline feature that can automatically detect branches in Git.

### Benefits

- If there's a new branch, no need to add a job manually
- If a branch is deleted, no need to delete the job manually
- Multi-branch pipeline can only create pipelines from Jenkinsfile, not directly in the job

- **Create multi-branch pipeline**

  - Go to Jenkins dashboard
  - Click **"New Item"**
  - Enter item name
  - Select **"Multi-branch Pipeline"** type
  - Click **"OK"**

- **Configure branch sources**

  - Click **"Add source"**
  - Select **"Git"**
  - **Repository URL:** Enter GitHub repository URL
  - **Credentials:** Select GitHub credentials

- **Configure build configuration**

  - **Mode:** Select **"by Jenkinsfile"**
  - **Script path:** Enter `Jenkinsfile`

- **Configure scan triggers**

  - Set desired interval for branch scanning under **"Scan Multi-branch Pipeline Triggers"**

- **Save and scan**

  - Click **"Save"**
  - Jenkins will automatically scan all branches in the GitHub repository
  - System will automatically add new jobs when new branches are created
  - Jobs will be automatically deleted when branches are deleted, according to the specified interval

---

## 💡 Best Practices

### Pipeline Organization

- Keep Jenkinsfile in version control
- Use descriptive stage names
- Keep stages focused and small
- Use shared libraries for common functionality

### Security

- Never hardcode credentials in Jenkinsfile
- Always use Jenkins credentials store
- Use single quotes for sensitive data in shell commands
- Limit submitters for input stages

### Performance

- Use parallel execution when possible
- Set appropriate timeouts
- Use agent labels to optimize resource usage
- Clean workspace when necessary

### Maintenance

- Document complex pipeline logic
- Use meaningful parameter names and descriptions
- Add comments for non-obvious code
- Keep pipeline syntax consistent across projects

---

## 🆘 Troubleshooting

- **Pipeline doesn't start**

  - Check agent availability
  - Verify agent labels match
  - Check Jenkins executor capacity

- **Credentials not working**

  - Verify credential ID is correct
  - Check credential type matches usage
  - Ensure proper quoting for sensitive data

- **Stage hangs**

  - Check for missing input approval
  - Verify timeout settings
  - Check for infinite loops in scripts

- **SCM polling not working**

  - Verify repository URL is accessible
  - Check credentials are valid
  - Ensure branch specifier is correct
  - Check SCM polling schedule syntax

---

## 📚 Additional Resources

- **Jenkins Pipeline Documentation:** https://www.jenkins.io/doc/book/pipeline/
- **Pipeline Syntax Reference:** https://www.jenkins.io/doc/book/pipeline/syntax/
- **Pipeline Steps Reference:** https://www.jenkins.io/doc/pipeline/steps/
- **Groovy Documentation:** https://groovy-lang.org/documentation.html

---

**Note:** This guide covers Jenkins Pipeline fundamentals. For advanced topics like shared libraries, custom DSL, and complex orchestration, refer to the official Jenkins documentation.
