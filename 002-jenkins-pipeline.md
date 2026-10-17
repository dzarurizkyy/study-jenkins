# ⚙️ Jenkins Pipeline

A practical reference guide for building Continuous Delivery pipelines in Jenkins with the declarative Groovy DSL — covering core concepts, agents, stages, steps, variables, credentials, parameters, triggers, conditions, parallel and matrix builds, and multi-branch pipelines.

---

## 📋 Table of Contents

- [Introduction](#-introduction)
  - [What Is Jenkins Pipeline?](#what-is-jenkins-pipeline)
  - [What Is a Continuous Delivery Pipeline?](#what-is-a-continuous-delivery-pipeline)
  - [Who Is This For?](#who-is-this-for)
  - [Why Use Pipeline?](#why-use-pipeline)
- [Installation](#-installation)
  - [Install the Pipeline Plugin](#install-the-pipeline-plugin)
- [Core Concepts](#-core-concepts)
  - [Pipeline, Agent, Stage, and Step](#pipeline-agent-stage-and-step)
  - [How the Concepts Fit Together](#how-the-concepts-fit-together)
- [Creating Pipeline Jobs](#-creating-pipeline-jobs)
  - [Create a Basic Pipeline Job](#create-a-basic-pipeline-job)
  - [Simple Pipeline Example](#simple-pipeline-example)
- [Pipeline with SCM](#-pipeline-with-scm)
  - [Set Up the Repository](#set-up-the-repository)
  - [Create a Pipeline Job from SCM](#create-a-pipeline-job-from-scm)
  - [Pipeline Script vs Pipeline Script from SCM](#pipeline-script-vs-pipeline-script-from-scm)
- [Agent Configuration](#-agent-configuration)
  - [Agent Types](#agent-types)
  - [Basic Agent Configuration](#basic-agent-configuration)
  - [Agent with Node Label](#agent-with-node-label)
  - [Add Labels to Jenkins Agents](#add-labels-to-jenkins-agents)
- [Post Actions](#-post-actions)
  - [What Is Post?](#what-is-post)
  - [Available Post Conditions](#available-post-conditions)
  - [Post Configuration Example](#post-configuration-example)
- [Stages](#-stages)
  - [What Are Stages?](#what-are-stages)
  - [How Stages Behave](#how-stages-behave)
  - [Multiple Stages Example](#multiple-stages-example)
  - [Pipeline Stage View Plugin](#pipeline-stage-view-plugin)
- [Steps](#-steps)
  - [What Are Steps?](#what-are-steps)
  - [Basic Steps](#basic-steps)
  - [Node and Process Steps](#node-and-process-steps)
- [Scripts](#-scripts)
  - [Why Use Scripts?](#why-use-scripts)
  - [Script Example](#script-example)
- [Utility Steps](#-utility-steps)
  - [What Are Utility Steps?](#what-are-utility-steps)
  - [Utility Steps Example](#utility-steps-example)
- [Agent Per Stage](#-agent-per-stage)
  - [When to Use Agent Per Stage](#when-to-use-agent-per-stage)
  - [Agent Per Stage Example](#agent-per-stage-example)
- [Global Variables](#-global-variables)
  - [What Are Global Variables?](#what-are-global-variables)
  - [Common Global Variables](#common-global-variables)
  - [Global Variable Example](#global-variable-example)
- [Environment Variables](#-environment-variables)
  - [Environment Variable Scope](#environment-variable-scope)
  - [Environment Variable Example](#environment-variable-example)
- [Credentials](#-credentials)
  - [The credentials() Helper](#the-credentials-helper)
  - [Supported Credential Types](#supported-credential-types)
  - [Credentials Example](#credentials-example)
  - [Handling Sensitive Information](#handling-sensitive-information)
- [Options](#-options)
  - [What Are Options?](#what-are-options)
  - [Common Options](#common-options)
  - [Options Example](#options-example)
- [Parameters](#-parameters)
  - [What Are Parameters?](#what-are-parameters)
  - [Parameter Types](#parameter-types)
  - [Parameters Example](#parameters-example)
- [Triggers](#-triggers)
  - [Trigger Types](#trigger-types)
  - [Trigger Example](#trigger-example)
- [Input](#-input)
  - [What Is Input?](#what-is-input)
  - [Input Options](#input-options)
  - [Input Example](#input-example)
  - [Parameters vs Input](#parameters-vs-input)
- [When Conditions](#-when-conditions)
  - [What Is When?](#what-is-when)
  - [Common When Conditions](#common-when-conditions)
  - [When Example](#when-example)
- [Sequential Stages](#-sequential-stages)
  - [What Are Sequential Stages?](#what-are-sequential-stages)
  - [Rules for Nested Stages](#rules-for-nested-stages)
  - [Sequential Stages Example](#sequential-stages-example)
- [Parallel Execution](#-parallel-execution)
  - [What Is Parallel Execution?](#what-is-parallel-execution)
  - [How Parallel Behaves](#how-parallel-behaves)
  - [Parallel Example](#parallel-example)
- [Matrix Builds](#-matrix-builds)
  - [What Is a Matrix?](#what-is-a-matrix)
  - [Matrix Cells](#matrix-cells)
  - [Matrix Example](#matrix-example)
  - [Exclude Matrix Cells](#exclude-matrix-cells)
- [Credential Binding](#-credential-binding)
  - [Why Use Credential Binding?](#why-use-credential-binding)
  - [Credential Binding Example](#credential-binding-example)
  - [credentials() vs withCredentials()](#credentials-vs-withcredentials)
- [Multi-Branch Pipeline](#-multi-branch-pipeline)
  - [What Is a Multi-Branch Pipeline?](#what-is-a-multi-branch-pipeline)
  - [Create a Multi-Branch Pipeline](#create-a-multi-branch-pipeline)
  - [Configure Branch Sources](#configure-branch-sources)
  - [Configure Build Configuration](#configure-build-configuration)
  - [Configure Scan Triggers](#configure-scan-triggers)
  - [Save and Scan](#save-and-scan)
- [Troubleshooting](#-troubleshooting)
- [Quick Reference](#-quick-reference)
- [Best Practices](#-best-practices)

---

## 🎯 Introduction

### What Is Jenkins Pipeline?

**Jenkins Pipeline** is a plugin that supports implementing continuous delivery pipelines in Jenkins. The pipeline is written as code (Groovy DSL), usually in a file called `Jenkinsfile`.

### What Is a Continuous Delivery Pipeline?

A continuous delivery pipeline is the set of commands that delivers software to users — from version control all the way to deployment.

### Who Is This For?

- **Backend Programmer** — Automates build, test, and deploy as part of the project's code
- **DevOps Engineer** — Designs reusable, reviewable CI/CD flows
- **Jenkins User** — Moves from clicking freestyle jobs to pipelines as code

### Why Use Pipeline?

- **Pipeline as code** — Since pipelines are written as code, stages are easy to modify and review
- **Stored with the project** — Pipelines usually live in a file inside the repository, so there's no fear of losing them when Jenkins restarts or fails
- **Versioned** — Every change to the pipeline is tracked in Git like any other code

---

## 📦 Installation

> **Analogy:** Jenkins out of the box is an empty factory floor. The Pipeline plugin is the conveyor belt system — without it, you can still do work by hand (freestyle jobs), but you can't build a proper assembly line.

### Install the Pipeline Plugin

Jenkins Pipeline is **not included** by default when installing Jenkins.

1. Go to the Jenkins dashboard
2. Click the **settings icon** (Manage Jenkins)
3. Select **"Plugins"**
4. Click **"Available plugins"**
5. Search for **"Pipeline"**
6. Install the **Pipeline: Aggregator** plugin
7. Check **"Restart Jenkins when installation is complete and no jobs are running"**

---

## 🧩 Core Concepts

> **Analogy:** A pipeline is a factory assembly line. The **agent** is the factory building where the line runs. Each **stage** is a station on the line (cutting, painting, packing), and each **step** is a single action a worker performs at that station.

### Pipeline, Agent, Stage, and Step

| Concept | Description |
| --- | --- |
| **Pipeline** | The complete definition of your continuous delivery code — all instructions for compile, test, deploy, etc. |
| **Agent** | A machine or server in the Jenkins infrastructure that executes the pipeline |
| **Stage** | A block that defines a task or phase (e.g., "Build", "Test", "Deploy"); shown in Jenkins as progress indicators. A pipeline usually contains many stages |
| **Step** | An individual instruction or command telling Jenkins what to do; steps run inside stages |

### How the Concepts Fit Together

```groovy
pipeline {            // Pipeline
  agent any           // Agent
  stages {
    stage("Build") {  // Stage
      steps {
        echo "Hi"     // Step
      }
    }
  }
}
```

---

## 🚀 Creating Pipeline Jobs

### Create a Basic Pipeline Job

1. Go to the Jenkins dashboard
2. Click **"New Item"**
3. Enter the job name
4. Select **"Pipeline"** as the item type
5. Click **"OK"**
6. Scroll to the **"Pipeline"** section
7. Select **"Pipeline script"** in the Definition dropdown

### Simple Pipeline Example

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

> **Analogy:** Instead of writing the assembly instructions on a whiteboard in the factory (Pipeline script), you keep the official instruction manual in the company archive (Git). Every time the line starts, the factory fetches the latest manual from the archive.

### Set Up the Repository

1. Select the project you want to integrate with Jenkins
2. Add a `Jenkinsfile` to the project root
3. Add the pipeline code to the `Jenkinsfile`
4. Commit and push to the repository:

   ```bash
   git init
   git add .
   git commit -m "commit message"
   git remote add origin GITHUB_URL
   git push -u origin BRANCH_NAME
   ```

### Create a Pipeline Job from SCM

1. Go to the Jenkins dashboard
2. Click **"New Item"**
3. Enter the job name
4. Select **"Pipeline"** as the item type
5. Click **"OK"**
6. Scroll to the **"Pipeline"** section
7. Select **"Pipeline script from SCM"** in the Definition dropdown
8. Fill in the form:

   | Field | Value |
   | --- | --- |
   | **SCM** | Git |
   | **Repository URL** | Your GitHub repository URL |
   | **Credentials** | Your GitHub credentials |
   | **Branch Specifier** | Branch name (e.g., `*/main`) |
   | **Script Path** | Jenkinsfile path (default: `Jenkinsfile`) |

9. Click **"Save"**
10. Click **"Build Now"**

### Pipeline Script vs Pipeline Script from SCM

| Aspect | Pipeline Script | Pipeline Script from SCM |
| --- | --- | --- |
| **Where the code lives** | Inside the Jenkins job config | `Jenkinsfile` in the Git repository |
| **Versioned** | No | Yes, with Git history |
| **Survives Jenkins failure** | Only if Jenkins data is backed up | Yes, it's in the repository |
| **Best for** | Quick experiments | Real projects |

---

## 🤖 Agent Configuration

> **Analogy:** The agent decides which factory building runs the assembly line. `any` means "whichever building is free," while a label means "only the building that has the right machines" (e.g., Linux with Java 11).

### Agent Types

The `agent` section determines where the pipeline executes.

| Type | Description |
| --- | --- |
| `any` | Pipeline runs on any available agent, including the master |
| `none` | No global agent is assigned; each stage must declare its own agent |
| `label` | Pipeline only runs on agents with the specified labels |
| `node` | Similar to `label`, but allows a custom workspace location |
| `docker` | Pipeline runs inside a Docker container |
| `dockerfile` | Like `docker`, but the image is built from a Dockerfile |
| `kubernetes` | Pipeline runs in a Kubernetes cluster |

### Basic Agent Configuration

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

### Agent with Node Label

Sometimes you want a pipeline to run only on specific agents — for example, agents running Fedora or Windows. Change the agent value to the desired label or node:

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

### Add Labels to Jenkins Agents

1. Go to the Jenkins dashboard
2. Select **"Build Executor Status"**
3. Click the node you want to label
4. Click **"Configure"**
5. In the **"Labels"** field, enter labels (space-separated for multiple)
6. Click **"Save"**

Example labels: `linux java11 docker`

> **Tip:** Label expressions support `&&` (and), `||` (or), and `!` (not), so `linux && java11` matches only agents that have **both** labels.

---

## 📢 Post Actions

> **Analogy:** When a product leaves the assembly line, a quality inspector decides what happens next. If it passed, ring the success bell. If it failed, call the supervisor. And no matter what, sweep the floor afterward.

### What Is Post?

`post` is a section that runs steps at the end of the pipeline when a condition is met — for example, sending a Slack or email message after the pipeline completes, or a notification when an error occurs.

### Available Post Conditions

| Condition | Runs When |
| --- | --- |
| `always` | Always, regardless of any condition |
| `changed` | The pipeline status changed from the previous build |
| `fixed` | The status changed from a previous failure to success |
| `regression` | The status changed from a previous success to failure |
| `aborted` | The pipeline was manually aborted |
| `failure` | The pipeline status is failure |
| `success` | The pipeline status is success |
| `cleanup` | Always, but after all other post conditions |

### Post Configuration Example

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

> **Analogy:** Stages are the stations on the assembly line, in order. A product moves from station to station — and if one station breaks the product, it never reaches the next station.

### What Are Stages?

`stages` is a section containing one or more `stage` blocks. Stages usually describe the phases of the continuous delivery pipeline.

### How Stages Behave

- **Sequential** — Stages execute in order
- **Stop on error** — If a stage fails, the following stages don't execute
- **Free naming** — No naming restrictions; name stages however you like
- **Common names** — Build, Test, Deploy

### Multiple Stages Example

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

### Pipeline Stage View Plugin

To see the progress of each stage visually, install the [Pipeline Stage View](https://plugins.jenkins.io/pipeline-stage-view/) plugin:

1. Install the plugin from the Jenkins Plugin Manager
2. View the stage visualization on the job page

---

## 🔧 Steps

> **Analogy:** Steps are the individual actions a worker performs at a station — pick up the part, tighten the bolt, wait ten seconds for the glue to dry.

### What Are Steps?

`steps` contains the instructions you want to execute in the pipeline.

### Basic Steps

Installing the Pipeline plugin automatically installs the **Pipeline: Basic Steps** plugin, which provides commonly used commands. See the [Basic Steps documentation](https://www.jenkins.io/doc/pipeline/steps/workflow-basic-steps/).

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

### Node and Process Steps

One of the most commonly used step plugins. It runs terminal commands, such as shell scripts (Unix) or command scripts (Windows). See the [`sh` documentation](https://www.jenkins.io/doc/pipeline/steps/workflow-durable-task-step/#sh-shell-script).

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

| Step | Platform |
| --- | --- |
| `sh` | Unix shell script |
| `bat` | Windows batch script |

---

## 💻 Scripts

> **Analogy:** The declarative pipeline is a fixed checklist. A `script` block is a small notebook where the worker is allowed to write their own logic — loops, conditions, variables — when the checklist isn't flexible enough.

### Why Use Scripts?

- Sometimes you need a very flexible pipeline, so Pipeline lets you insert Groovy code
- By default, writing Groovy code directly inside `steps` causes an error
- Wrap the code in a `script` block to mark that it contains Groovy code

### Script Example

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

> **Tip:** Keep `script` blocks small. If a pipeline needs a lot of Groovy, it's usually a sign the logic belongs in a shell script or a shared library.

---

## 🧰 Utility Steps

### What Are Utility Steps?

A plugin with utilities that simplify pipeline creation — for example reading files, creating archives, and generating hashes. See the [Pipeline Utility Steps documentation](https://www.jenkins.io/doc/pipeline/steps/pipeline-utility-steps/).

### Utility Steps Example

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

## 🏭 Agent Per Stage

> **Analogy:** Some products need parts made in different buildings — the metal frame in the welding building, the electronics in the clean room. Instead of one building for the whole line, each station picks the building it needs.

### When to Use Agent Per Stage

By default, all stages run on the pipeline's agent. When stages need different environments — for example, the first stage needs a Java agent and the second needs a Golang agent — set the pipeline agent to `none` and add an agent to each stage.

### Agent Per Stage Example

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

### What Are Global Variables?

Because pipelines are written in Groovy, Jenkins exposes global variables that are automatically accessible in the pipeline — no manual lookup needed. The full list is available at:

```text
JENKINS_URL/job/JOB_NAME/pipeline-syntax/globals
```

### Common Global Variables

| Variable | Description |
| --- | --- |
| `env` | Global information from Jenkins (job name, build number, etc.) |
| `currentBuild` | Information about the currently running build |
| `params` | Values of the pipeline parameters (see [Parameters](#-parameters)) |

### Global Variable Example

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

## 🔤 Environment Variables

> **Analogy:** An announcement on the factory-wide speaker reaches every station. A note taped to one station's workbench is only seen by that station. Environment variables work the same way: pipeline-level is the speaker, stage-level is the taped note.

### Environment Variable Scope

| Defined At | Accessible From |
| --- | --- |
| **Pipeline level** | All stages |
| **Stage level** | Only that stage |

### Environment Variable Example

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

> **Analogy:** Instead of writing the vault combination on the instruction manual, the manual just says "get the combination from the security office." Anyone reading the manual (the `Jenkinsfile` in Git) never sees the real secret.

### The credentials() Helper

Inside the `environment` section, the special `credentials()` command retrieves data from the Jenkins credentials store. This is more secure than typing secrets into the `Jenkinsfile`.

### Supported Credential Types

| Type | Behavior |
| --- | --- |
| **Secret text** | The variable contains the secret text value |
| **Secret file** | The variable contains the path to a temporary secret file (deleted automatically after the pipeline completes) |
| **Username and password** | The variable contains `username:password`, and `VARNAME_USR` and `VARNAME_PSW` are created automatically |
| **SSH with private key** | The variable contains the path to a temporary SSH key file; `VARNAME_USR` and `VARNAME_PSW` (the SSH passphrase) are created automatically |

### Credentials Example

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

### Handling Sensitive Information

The `${key}` syntax inside double quotes is **Groovy string interpolation** and should not be used for sensitive data. To keep secrets hidden, use single quotes `''` with `$KEY`, so the value is expanded by the shell instead of by Groovy.

| | Code |
| --- | --- |
| ✅ **Correct** | `sh('echo "Password: $APP_PSW" > "secret.txt"')` |
| ❌ **Incorrect** (exposes data) | `sh("echo 'Password: ${APP_PSW}' > 'secret.txt'")` |

> **Gotcha:** With double quotes, Groovy inserts the secret into the command string *before* it runs — so the secret can leak into logs and process lists. Jenkins even prints a warning about insecure interpolation.

---

## 📌 Options

### What Are Options?

`options` configures pipeline settings. Like `environment`, options can be set at the pipeline level or at the stage level. See the [options documentation](https://www.jenkins.io/doc/book/pipeline/syntax/#options).

### Common Options

| Option | Description |
| --- | --- |
| `disableConcurrentBuilds()` | Prevents concurrent builds of the same pipeline |
| `timeout(time: 10, unit: 'MINUTES')` | Sets a pipeline timeout |
| `retry(3)` | Retries on failure |
| `timestamps()` | Adds timestamps to the console output |

### Options Example

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

> **Analogy:** Parameters are the order form filled in before production starts — color, size, quantity. The assembly line is the same; the form decides what kind of product comes out.

### What Are Parameters?

The `parameters` command adds inputs to the pipeline. Values entered by the user are stored automatically in the `params` global variable.

### Parameter Types

| Type | Description |
| --- | --- |
| `string` | Text/string parameter |
| `text` | Like string, but with a multi-line text area |
| `booleanParam` | Boolean parameter (true/false) |
| `choice` | String parameter with predefined options |
| `password` | String parameter treated as sensitive |

### Parameters Example

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

> **Analogy:** Triggers decide when the assembly line switches on by itself — at a fixed time every day (`cron`), whenever a new batch of raw material arrives (`pollSCM`), or right after another factory finishes its batch (`upstream`).

### Trigger Types

| Type | Description |
| --- | --- |
| `cron` | Runs the job automatically based on a cron expression |
| `pollSCM` | Checks the SCM for changes on a cron schedule; runs the job only if there are changes |
| `upstream` | Runs the job after another job completes with a specific result |

### Trigger Example

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

> **Analogy:** Before the finished product is shipped, the line stops at a checkpoint and waits for the manager to sign off. Nothing moves until an authorized person presses "approve."

### What Is Input?

`input` is similar to parameters, but it's added to a **stage**. The stage won't run until a user fills in the input.

### Input Options

| Option | Description |
| --- | --- |
| `id` | Input identifier; defaults to the stage name |
| `message` | Message displayed to the user |
| `ok` | Text for the OK button |
| `submitter` | Users allowed to submit (comma-separated for multiple) |
| `parameters` | Parameters the user needs to fill in |

### Input Example

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

### Parameters vs Input

| Aspect | Parameters | Input |
| --- | --- | --- |
| **Defined at** | Pipeline level | Stage level |
| **Asked when** | Before the build starts | When the build reaches that stage |
| **Accessed via** | `params.NAME` | The parameter name directly (e.g., `TARGET_ENV`) |
| **Best for** | Configuring the whole build | Manual approval gates (e.g., deploy to production) |

---

## ❓ When Conditions

> **Analogy:** Some stations only activate for certain orders — the gift-wrapping station only runs when the customer ticked "gift." `when` is that tick box for a stage.

### What Is When?

`when` determines under which conditions a stage is executed. See the [when documentation](https://www.jenkins.io/doc/book/pipeline/syntax/#when).

### Common When Conditions

| Condition | Runs the Stage When |
| --- | --- |
| `branch` | The build is on a specific branch |
| `expression` | The expression returns true |
| `environment` | An environment variable matches a value |
| `equals` | Two values are equal |

### When Example

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

### What Are Sequential Stages?

A stage can contain its own `stages`. Nested stages execute sequentially by default.

### Rules for Nested Stages

- A stage can only have **one** of these: `steps`, `stages`, `parallel`, or `matrix`
- If a stage contains `stages`, it can't also contain `steps`

### Sequential Stages Example

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

> **Analogy:** Instead of one worker painting the doors and then the wheels, two workers paint them at the same time. The product is finished faster — but if one worker ruins their part, you can choose to stop the other one immediately.

### What Is Parallel Execution?

In some cases you want stages to run at the same time instead of one after another. `parallel` runs its child stages concurrently.

### How Parallel Behaves

- **Waits for all** — By default, parallel waits for every stage to finish, even if one fails
- **Fail fast** — Add `parallelAlwaysFailFast()` to `options` to stop all stages as soon as one fails
- **Agent per child** — You can't add an agent to the parent stage, so specify it in each parallel stage

### Parallel Example

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

> **Analogy:** A shoe factory makes every model in every size. Instead of writing a separate line for each model-size pair, you list the models and the sizes once, and the factory runs every combination in parallel.

### What Is a Matrix?

A `matrix` defines a multi-dimensional set of name-value combinations and runs a stage for each combination **in parallel**.

- **Powerful** — Runs stages in parallel across every predetermined combination
- **Fail fast supported** — Like `parallel`, you can use `failFast` or `parallelAlwaysFailFast()`

### Matrix Cells

Each combination of axis values is called a **matrix cell**. With two axes, `OS` and `ARC`, you get:

| OS | ARC 32 | ARC 64 |
| --- | --- | --- |
| **linux** | linux 32 | linux 64 |
| **windows** | windows 32 | windows 64 |
| **mac** | mac 32 | mac 64 |

Axis values are available as environment variables inside the stage (e.g., `${OS}`, `${ARC}`).

### Matrix Example

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

### Exclude Matrix Cells

Use `excludes` to skip specific cells — for example, excluding **mac 32** because that version no longer exists:

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

## 🔒 Credential Binding

> **Analogy:** `credentials()` hands the vault combination to the whole factory for the entire shift. `withCredentials()` hands it to one worker for one task, and takes it back the moment the task is done.

### Why Use Credential Binding?

`credentials()` exposes secrets as environment variables for the whole pipeline or stage. Sometimes you only want a credential inside a specific block. `withCredentials()` binds it only for that block. See the [Credentials Binding documentation](https://www.jenkins.io/doc/pipeline/steps/credentials-binding/).

### Credential Binding Example

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

### credentials() vs withCredentials()

| Aspect | `credentials()` | `withCredentials()` |
| --- | --- | --- |
| **Declared in** | `environment` section | `steps`, as a block |
| **Scope** | Whole pipeline or stage | Only inside the block |
| **Variable names** | Fixed (`VAR`, `VAR_USR`, `VAR_PSW`) | Chosen by you (`usernameVariable`, `passwordVariable`) |
| **Best for** | Secrets used across many steps | Secrets needed for one specific action |

---

## 🌿 Multi-Branch Pipeline

> **Analogy:** Instead of building one assembly line for one product version, the factory watches the design office. Every time a new design draft (branch) appears, a new line is set up automatically — and when a draft is thrown away, its line is dismantled.

### What Is a Multi-Branch Pipeline?

A regular pipeline job only builds one predetermined branch. In reality you want the pipeline to run on **every** branch in the repository. A multi-branch pipeline automatically detects branches in Git and creates a job for each one.

### Create a Multi-Branch Pipeline

1. Go to the Jenkins dashboard
2. Click **"New Item"**
3. Enter the item name
4. Select the **"Multi-branch Pipeline"** type
5. Click **"OK"**

### Configure Branch Sources

1. Click **"Add source"**
2. Select **"Git"**
3. **Repository URL:** Enter the GitHub repository URL
4. **Credentials:** Select the GitHub credentials

### Configure Build Configuration

| Field | Value |
| --- | --- |
| **Mode** | by Jenkinsfile |
| **Script path** | `Jenkinsfile` |

### Configure Scan Triggers

Set the desired branch-scanning interval under **"Scan Multi-branch Pipeline Triggers"**.

### Save and Scan

1. Click **"Save"**
2. Jenkins automatically scans all branches in the GitHub repository
3. New jobs are added automatically when new branches are created
4. Jobs are deleted automatically when branches are deleted, according to the scan interval

> **Tip:** In a multi-branch pipeline, `env.BRANCH_NAME` is set for each branch — combine it with `when { branch "main" }` to deploy only from the main branch.

---

## 🆘 Troubleshooting

| Problem | What to Check |
| --- | --- |
| **Pipeline doesn't start** | Agent availability · Agent labels match · Jenkins executor capacity |
| **Credentials not working** | Credential ID is correct · Credential type matches usage · Proper quoting for sensitive data |
| **Stage hangs** | Missing input approval · Timeout settings · Infinite loops in scripts |
| **SCM polling not working** | Repository URL is accessible · Credentials are valid · Branch specifier is correct · Polling schedule syntax |

---

## 🎯 Quick Reference

| Directive | Purpose | Level |
| --- | --- | --- |
| `agent` | Where the pipeline or stage runs | Pipeline / Stage |
| `stages` / `stage` | Phases of the pipeline, executed in order | Pipeline / Stage |
| `steps` | Commands executed inside a stage | Stage |
| `script` | Run Groovy code inside steps | Steps |
| `post` | Steps run after completion, based on the result | Pipeline / Stage |
| `environment` | Define environment variables (and `credentials()`) | Pipeline / Stage |
| `options` | Pipeline settings (timeout, concurrency, fail fast) | Pipeline / Stage |
| `parameters` | User inputs before the build starts | Pipeline |
| `triggers` | Start the job automatically (`cron`, `pollSCM`, `upstream`) | Pipeline |
| `input` | Pause a stage until a user approves | Stage |
| `when` | Run a stage only if a condition is met | Stage |
| `parallel` | Run child stages at the same time | Stage |
| `matrix` | Run a stage for every axis combination | Stage |
| `withCredentials` | Bind credentials only inside a block | Steps |

---

## 💡 Best Practices

**✅ Do This**

- **Keep the `Jenkinsfile` in version control** — use [Pipeline Script from SCM](#create-a-pipeline-job-from-scm) for real projects
- **Use descriptive stage names** and keep stages focused and small
- **Use shared libraries** for functionality reused across pipelines
- **Always use the Jenkins credentials store** for secrets
- **Use single quotes for sensitive data in shell commands** — see [Handling Sensitive Information](#handling-sensitive-information)
- **Prefer `withCredentials()`** when a secret is needed for only one action
- **Limit `submitter`** on input stages so only authorized users can approve
- **Use parallel execution** when stages are independent
- **Set appropriate timeouts** so a stuck stage doesn't block an executor forever
- **Use agent labels** to optimize resource usage
- **Clean the workspace** when necessary
- **Document complex logic** and add comments for non-obvious code
- **Use meaningful parameter names and descriptions**
- **Keep pipeline syntax consistent** across projects

**❌ Avoid This**

- **Hardcoding credentials in the `Jenkinsfile`** — anyone with repository access can read them
- **Using Groovy interpolation (`"${SECRET}"`) for secrets** — the value can leak into logs
- **Putting large amounts of Groovy in `script` blocks** — move complex logic to shell scripts or shared libraries
- **Running long stages without a timeout** — they can hang and hold an executor indefinitely
- **Leaving input stages open to everyone** — anyone could approve a production deploy

> Reference: [Jenkins Pipeline Documentation](https://www.jenkins.io/doc/book/pipeline/) · [Pipeline Syntax](https://www.jenkins.io/doc/book/pipeline/syntax/) · [Pipeline Steps Reference](https://www.jenkins.io/doc/pipeline/steps/)
