# 📚 Jenkins Shared Library

A practical reference guide for creating and using Jenkins Shared Libraries — covering folder structure, registration, global variables, Groovy classes, parameters, library resources, and full declarative pipelines reused across multiple projects.

---

## 📋 Table of Contents

- [Introduction](#-introduction)
  - [What Is a Shared Library?](#what-is-a-shared-library)
  - [Who Is This For?](#who-is-this-for)
  - [Why Use a Shared Library?](#why-use-a-shared-library)
  - [Requirements](#requirements)
  - [Prerequisites](#prerequisites)
- [Creating a Shared Library](#-creating-a-shared-library)
  - [How to Create a Shared Library](#how-to-create-a-shared-library)
- [Folder Structure](#-folder-structure)
  - [Required Structure](#required-structure)
  - [Directory Descriptions](#directory-descriptions)
- [Registering a Shared Library](#-registering-a-shared-library)
  - [Register the Library in Jenkins](#register-the-library-in-jenkins)
  - [Library Settings](#library-settings)
- [Using a Shared Library](#-using-a-shared-library)
  - [Groovy Pipeline Plugin](#groovy-pipeline-plugin)
  - [The @Library Annotation](#the-library-annotation)
  - [Auto-Import vars Files](#auto-import-vars-files)
- [Global Variables](#-global-variables)
  - [How Global Variables Work](#how-global-variables-work)
  - [Defining a Function](#defining-a-function)
  - [Multiple Functions](#multiple-functions)
  - [Default Function (call)](#default-function-call)
- [Groovy Classes](#-groovy-classes)
  - [Creating a Groovy Class](#creating-a-groovy-class)
  - [Groovy vs Jenkins Pipeline Output](#groovy-vs-jenkins-pipeline-output)
  - [Accessing Pipeline Steps in Classes](#accessing-pipeline-steps-in-classes)
  - [Global Variables vs Classes](#global-variables-vs-classes)
- [Parameters](#-parameters)
  - [String Parameters](#string-parameters)
  - [List Parameters](#list-parameters)
  - [Map Parameters](#map-parameters)
  - [Choosing a Parameter Type](#choosing-a-parameter-type)
- [Library Resources](#-library-resources)
  - [What Are Library Resources?](#what-are-library-resources)
  - [Accessing Resources](#accessing-resources)
  - [Example: Reading a JSON Resource](#example-reading-a-json-resource)
- [Declarative Pipelines](#-declarative-pipelines)
  - [Pipeline as a Library Function](#pipeline-as-a-library-function)
  - [Basic Example](#basic-example)
  - [Conditional Pipelines](#conditional-pipelines)
- [Complete Library Structure](#-complete-library-structure)
- [Troubleshooting](#-troubleshooting)
- [Quick Reference](#-quick-reference)
- [Best Practices](#-best-practices)

---

## 🎯 Introduction

### What Is a Shared Library?

A **Jenkins Shared Library** is a Jenkins Pipeline feature that lets you store pipeline code in a **separate repository** from the projects that use it.

> **Analogy:** Think of a franchise restaurant chain. Every branch doesn't invent its own recipes — they all follow one central recipe book from headquarters. When headquarters improves a recipe, every branch gets the update. The shared library is that central recipe book, and each project's `Jenkinsfile` is a branch that just says "cook recipe #3."

### Who Is This For?

- **DevOps Engineer** — Standardizes CI/CD across many repositories
- **Backend Programmer** — Keeps `Jenkinsfile`s short and focused on the project
- **Platform Team** — Maintains one source of truth for build and deploy logic

### Why Use a Shared Library?

- **Centralized management** — No need to write pipelines manually for each project
- **Code reusability** — One central repository contains the pipeline code
- **Easy maintenance** — Update pipeline code in one place, and it applies to all projects
- **Consistency** — All projects use standardized pipeline configurations

### Requirements

- A Jenkins Shared Library requires an **SCM (Source Control Management)** system such as Git
- The shared library is stored in a **Git repository**

### Prerequisites

- Jenkins installed with the **Pipeline** plugin
- A Git repository for storing the shared library
- Basic knowledge of the **Groovy** programming language
- Access to Jenkins configuration (**admin rights**)

---

## 🚀 Creating a Shared Library

### How to Create a Shared Library

1. Create a folder with the required structure (see [Folder Structure](#-folder-structure))
2. Add Groovy code files following that structure
3. Store the folder in a Git repository
4. Jenkins uses Git to access and use the shared library

---

## 📁 Folder Structure

> **Analogy:** The recipe book has three chapters. **vars/** is the list of ready-to-use recipes anyone can call by name. **src/** is the technical chapter with detailed techniques for advanced cooks. **resources/** is the appendix with ingredient lists and reference sheets.

### Required Structure

Your shared library must follow this folder structure:

```text
jenkins-shared-library/
├── resources/          # Non-code files (text, JSON, images, etc.)
├── src/                # Groovy class files
│   └── your/package/   # Package structure
└── vars/               # Global variable Groovy files
```

### Directory Descriptions

| Directory | Contents | Accessed Via |
| --- | --- | --- |
| **resources/** | Non-code files like text, JSON, images, and other assets | `libraryResource("path")` |
| **src/** | Groovy class files organized in a package structure | `import your.package.ClassName` |
| **vars/** | Groovy files that become global variables | File name (e.g., `hello.world()`) |

---

## 🔧 Registering a Shared Library

### Register the Library in Jenkins

Before using a shared library, you must register it in Jenkins:

1. Log in to Jenkins
2. Click **Manage Jenkins** on the dashboard
3. Select **Configure System** (called **System** in newer Jenkins versions)
4. Scroll down to the **Global Trusted Pipeline Libraries** section
5. Click **Add**
6. Fill in the library settings (see the table below)
7. Click **Save** to complete registration

### Library Settings

| Field | Value |
| --- | --- |
| **Name** | Library name — best practice: match the repository name |
| **Default version** | Branch or tag (e.g., `main`) |
| **Retrieval method** | Modern SCM |
| **Source Code Management** | Git |
| **Project Repository** | Git repository URL |
| **Credentials** | Select or add GitHub credentials |

> **Note:** The **Name** registered here is the name you use in `@Library("...")`. If they don't match, Jenkins can't find the library.

---

## 🎯 Using a Shared Library

### Groovy Pipeline Plugin

Jenkins Shared Library relies on the [Pipeline: Groovy Libraries](https://plugins.jenkins.io/pipeline-groovy-lib/) plugin, which is installed by default with Jenkins Pipeline.

### The @Library Annotation

Use the `@Library` annotation to import a shared library.

Syntax:

```groovy
@Library("library-name")
@Library("library-name@tag")
@Library("library-name@branch")
```

Examples:

```groovy
// Use default version
@Library('jenkins-shared-library')

// Use specific branch
@Library('jenkins-shared-library@main')

// Use specific tag
@Library('jenkins-shared-library@v1.0.0')
```

| Form | Version Used |
| --- | --- |
| `@Library('name')` | The **Default version** set during registration |
| `@Library('name@branch')` | The latest commit on that branch |
| `@Library('name@tag')` | That exact tagged release |

### Auto-Import vars Files

Add an underscore `_` after `@Library` to import all files from the `vars/` folder:

```groovy
@Library('jenkins-shared-library@main') _
```

This makes every file in `vars/` available as a global variable without explicit imports.

> **Gotcha:** An annotation in Groovy must be attached to something. When you don't have an `import` statement right after `@Library`, the `_` is what it attaches to — leave it out and the `Jenkinsfile` fails to compile.

---

## 🌐 Global Variables

> **Analogy:** Each file in `vars/` is a named recipe card. The card's name is how you order it (`hello`), and each section on the card is a variation you can pick (`hello.world()`). Saying just the card's name without a variation gives you the house special — the `call()` function.

### How Global Variables Work

Files in the `vars/` folder automatically become global variables in your `Jenkinsfile`.

- **File name = variable name**

  | File | Global Variable |
  | --- | --- |
  | `vars/author.groovy` | `author` |
  | `vars/hello.groovy` | `hello` |

- **Everything is exposed** — All functions, variables, and classes in the file are accessible through the global variable

### Defining a Function

`vars/hello.groovy`

```groovy
def world() {
  echo("Hello World")
}
```

`Jenkinsfile`

```groovy
@Library('jenkins-shared-library@main') _

pipeline {
  agent any
  stages {
    stage("Hello World") {
      steps {
        script {
          hello.world()
        }
      }
    }
  }
}
```

### Multiple Functions

`vars/author.groovy`

```groovy
def name() {
  "Dzaru Rizky Fathan Fortuna"
}

def channel() {
  "dzarurizky"
}
```

`Jenkinsfile`

```groovy
@Library('jenkins-shared-library@main') _

pipeline {
  agent any
  stages {
    stage("Author Info") {
      steps {
        script {
          echo(author.name())
          echo(author.channel())
        }
      }
    }
  }
}
```

### Default Function (call)

The `call()` function is invoked when you use the global variable itself as a function:

`vars/author.groovy`

```groovy
def call() {
  "Junior Full Stack Developer"
}

def name() {
  "Dzaru Rizky Fathan Fortuna"
}

def channel() {
  "dzarurizky"
}
```

`Jenkinsfile`

```groovy
@Library('jenkins-shared-library@main') _

pipeline {
  agent any
  stages {
    stage("Author Info") {
      steps {
        script {
          echo(author())           // Calls call() function
          echo(author.name())
          echo(author.channel())
        }
      }
    }
  }
}
```

| Usage | Function Called |
| --- | --- |
| `author()` | `call()` |
| `author.name()` | `name()` |
| `author.channel()` | `channel()` |

---

## 🎓 Groovy Classes

> **Analogy:** Global variables are recipe cards anyone can grab from the counter. Groovy classes are specialized kitchen tools kept in a labeled drawer — more structured and reusable, but you have to open the drawer (`import`) and hand them the stove (`steps`) before they can actually cook anything.

### Creating a Groovy Class

You can create Groovy classes in the `src/` directory for more structured code.

`src/dzarurizkyy/jenkins/Output.groovy`

```groovy
package dzarurizkyy.jenkins

class Output {
  static def hello(String name) {
    println("Hello ${name}")
  }
}
```

`Jenkinsfile`

```groovy
@Library('jenkins-shared-library@main') _

import dzarurizkyy.jenkins.Output

pipeline {
  agent any
  stages {
    stage("Login") {
      steps {
        script {
          Output.hello("Dzaru Rizky")
        }
      }
    }
  }
}
```

### Groovy vs Jenkins Pipeline Output

| Context | Function |
| --- | --- |
| **Jenkins Pipeline** | `echo()` |
| **Groovy** | `println()` |

### Accessing Pipeline Steps in Classes

To use Jenkins Pipeline steps inside a Groovy class, pass the `steps` object:

`src/dzarurizkyy/jenkins/Output.groovy`

```groovy
package dzarurizkyy.jenkins

class Output {
  static def hello(steps, String name) {
    steps.echo("Hello ${name}")
  }
}
```

`Jenkinsfile`

```groovy
@Library('jenkins-shared-library@main') _

import dzarurizkyy.jenkins.Output

pipeline {
  agent any
  stages {
    stage("Login") {
      steps {
        script {
          Output.hello(this, "Dzaru Rizky")
        }
      }
    }
  }
}
```

> **Note:** `this` in the `Jenkinsfile` is the pipeline script itself, so passing it gives the class access to every pipeline step (`echo`, `sh`, `libraryResource`, and so on).

### Global Variables vs Classes

| Aspect | Global Variables (`vars/`) | Classes (`src/`) |
| --- | --- | --- |
| **Access to pipeline steps** | Direct (`echo`, `sh`, ...) | Only through a passed `steps` object |
| **Import needed** | No (with `@Library(...) _`) | Yes (`import package.Class`) |
| **Best for** | Pipeline-related scripts | Complex logic and reusable classes |

**Recommendation:** Use global variables (`vars/`) when you need Jenkins Pipeline features.

---

## 📊 Parameters

> **Analogy:** A recipe can take a special request. A single note ("extra spicy") is a String. A shopping list of toppings is a List. A full order form with labeled fields (name, table, allergies) is a Map.

Functions in shared libraries accept parameters just like regular Groovy functions.

### String Parameters

`vars/command.groovy`

```groovy
def call(String command) {
  sh("echo 'this is ${command}' > command.txt")
}
```

`Jenkinsfile`

```groovy
@Library('jenkins-shared-library@main') _

pipeline {
  agent any
  stages {
    stage("Parameter") {
      steps {
        script {
          command("function call")
        }
      }
    }
  }
}
```

### List Parameters

Use List parameters for a variable number of arguments:

`vars/command.groovy`

```groovy
def call(List commands) {
  for (command in commands) {
    sh("echo ${command}")
  }
}
```

`Jenkinsfile`

```groovy
@Library('jenkins-shared-library@main') _

pipeline {
  agent any
  stages {
    stage("Parameter") {
      steps {
        script {
          command(["Test 1", "Test 2", "Test 3"])
        }
      }
    }
  }
}
```

### Map Parameters

Map parameters are ideal for complex key-value data:

`vars/hello.groovy`

```groovy
def person(Map person) {
  echo("Hello ${person.firstName} ${person.lastName}")
}
```

`Jenkinsfile`

```groovy
@Library('jenkins-shared-library@main') _

pipeline {
  agent any
  stages {
    stage("Parameter") {
      steps {
        script {
          hello.person([
            "firstName": "Rakuro",
            "lastName": "Hizutome"
          ])
        }
      }
    }
  }
}
```

### Choosing a Parameter Type

| Type | Best For | Example Call |
| --- | --- | --- |
| **String** | A single value | `command("function call")` |
| **List** | A variable number of similar values | `command(["Test 1", "Test 2"])` |
| **Map** | Named options / configuration | `hello.person([firstName: "Rakuro"])` |

---

## 📦 Library Resources

### What Are Library Resources?

The `resources/` folder stores non-code files such as text, JSON, images, and other assets.

### Accessing Resources

Use `libraryResource(location)` to read a file from the `resources/` folder:

```groovy
libraryResource("path/to/file")
```

The function returns the file content as **text**.

### Example: Reading a JSON Resource

`resources/config/build.json`

```json
{
  "app": "Jenkins Shared Library",
  "author": "Dzaru Rizky Fathan Fortuna"
}
```

`Jenkinsfile`

```groovy
@Library('jenkins-shared-library@main') _

pipeline {
  agent any
  stages {
    stage("Library Resource") {
      steps {
        script {
          def config = libraryResource("config/build.json")
          echo(config)
        }
      }
    }
  }
}
```

Output:

```text
{
  "app": "Jenkins Shared Library",
  "author": "Dzaru Rizky Fathan Fortuna"
}
```

> **Tip:** Since `libraryResource` returns plain text, combine it with `readJSON(text: config)` from Pipeline Utility Steps if you need to access fields like `config.app`.

---

## 🔄 Declarative Pipelines

> **Analogy:** Instead of handing each branch a recipe book and letting them assemble the meal, headquarters ships a complete meal kit. The branch just opens the box and says "serve." The `Jenkinsfile` shrinks to a single line.

### Pipeline as a Library Function

Shared libraries can define a **complete declarative pipeline** inside a `vars/` file, which makes each project's `Jenkinsfile` very small.

### Basic Example

`vars/declarative.groovy`

```groovy
def call(Map setup) {
  pipeline {
    agent any
    stages {
      stage("Parameter") {
        steps {
          script {
            hello.person([
              "firstName": "Rakuro",
              "lastName": "Hizutome"
            ])
            command(["Test 1", "Test 2", "Test 3"])
          }
        }
      }
      stage("Library Resource") {
        steps {
          script {
            def config = libraryResource("config/build.json")
            echo(config)
          }
        }
      }
      stage("Hello World") {
        steps {
          script {
            echo(author())
            echo(author.name())
            echo(author.channel())
          }
        }
      }
    }
  }
}
```

`Jenkinsfile`

```groovy
@Library('jenkins-shared-library@main') _

declarative([:])
```

### Conditional Pipelines

Add logic to choose which pipeline to run based on the setup map:

`vars/declarative.groovy`

```groovy
def call(Map setup) {
  if (setup.type == "start") {
    pipeline {
      agent any
      stages {
        stage("Parameter") {
          steps {
            script {
              hello.person([
                "firstName": "Rakuro",
                "lastName": "Hizutome"
              ])
              command(["Test 1", "Test 2", "Test 3"])
            }
          }
        }
        stage("Library Resource") {
          steps {
            script {
              def config = libraryResource("config/build.json")
              echo(config)
            }
          }
        }
        stage("Hello World") {
          steps {
            script {
              echo(author())
              echo(author.name())
              echo(author.channel())
            }
          }
        }
      }
    }
  } else {
    pipeline {
      agent any
      stages {
        stage("Unsupported type") {
          steps {
            script {
              echo("Unsupported type")
            }
          }
        }
      }
    }
  }
}
```

`Jenkinsfile`

```groovy
@Library('jenkins-shared-library@main') _

// Use "start" type
declarative([type: "start"])

// Or use different type
declarative([type: "stop"])
```

> **Gotcha:** Only **one** `pipeline { }` block can run per build. Call `declarative(...)` once in a `Jenkinsfile` — the two calls above are alternatives, not a sequence.

---

## 🗂 Complete Library Structure

```text
jenkins-shared-library/
├── resources/
│   └── config/
│       └── build.json
├── src/
│   └── dzarurizkyy/
│       └── jenkins/
│           └── Output.groovy
└── vars/
    ├── author.groovy
    ├── command.groovy
    ├── declarative.groovy
    └── hello.groovy
```

---

## 🆘 Troubleshooting

| Problem | What to Check |
| --- | --- |
| **Library not found** | Library is registered in Jenkins · Name matches the `@Library` annotation · Git repository is accessible |
| **Import errors** | Package structure in `src/` · Import statements match package names · Class names match file names |
| **Function not available** | File exists in `vars/` · No syntax errors in Groovy files · `_` is added after `@Library` |
| **Permission issues** | Jenkins has read access to the Git repository · Credentials are configured · Branch/tag exists in the repository |

---

## 🎯 Quick Reference

| Feature | Location / Syntax | Purpose |
| --- | --- | --- |
| **Register library** | Manage Jenkins → System → Global Trusted Pipeline Libraries | Make the library available to pipelines |
| **Import library** | `@Library('name@version') _` | Load the library in a `Jenkinsfile` |
| **Global variable** | `vars/name.groovy` → `name.fn()` | Reusable pipeline functions |
| **Default function** | `def call()` → `name()` | Use the variable itself as a function |
| **Groovy class** | `src/pkg/Class.groovy` → `import pkg.Class` | Structured, complex logic |
| **Pipeline steps in a class** | Pass `this` as `steps` | Use `echo`, `sh`, etc. inside classes |
| **Parameters** | String · List · Map | Pass data into library functions |
| **Library resource** | `libraryResource("path")` | Read non-code files as text |
| **Declarative pipeline** | `vars/declarative.groovy` with `pipeline { }` | Reuse an entire pipeline in one line |

---

## 💡 Best Practices

**✅ Do This**

- **Match the library name to the repository name** when registering it
- **Use descriptive names** for files and functions
- **Follow Java/Groovy package naming conventions** for `src/`
- **Use lowercase with hyphens** for repository names
- **Keep `vars/` files simple and focused** — one responsibility per file
- **Use `src/` for complex logic** and reusable classes
- **Store configuration files in `resources/`** instead of hardcoding them
- **Pin a version with semantic versioning tags** (`v1.0.0`, `v2.0.0`) for stable projects
- **Use `main` or `master` for stable code** and feature branches for development
- **Test library changes before tagging**, and validate in non-production environments first
- **Document functions** — add comments, describe parameters and return values, and maintain a `CHANGELOG.md`

**❌ Avoid This**

- **Making big changes directly on the default branch** — every project using the default version picks them up immediately
- **Forgetting the `_` after `@Library`** when there's no import below it
- **Using `println()` in classes and expecting it in the console** — pass `steps` and use `steps.echo()` instead
- **Putting pipeline-step-heavy logic in `src/` classes** — use `vars/` where steps are directly available
- **Hardcoding project-specific values in the library** — pass them as parameters instead

> Reference: [Jenkins — Extending with Shared Libraries](https://www.jenkins.io/doc/book/pipeline/shared-libraries/) · [Pipeline: Groovy Libraries Plugin](https://plugins.jenkins.io/pipeline-groovy-lib/)
