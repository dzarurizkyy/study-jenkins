## 📚 Jenkins Shared Library Guide

A comprehensive guide for creating and using Jenkins Shared Libraries to centralize and reuse pipeline code across multiple projects.

-----

## 📋 Table of Contents

  - [What is Shared Library](https://www.google.com/search?q=%23-what-is-shared-library)
  - [Why Use Shared Library](https://www.google.com/search?q=%23-why-use-shared-library)
  - [Prerequisites](https://www.google.com/search?q=%23-prerequisites)
  - [Creating Shared Library](https://www.google.com/search?q=%23-creating-shared-library)
  - [Folder Structure](https://www.google.com/search?q=%23-folder-structure)
  - [Registering Shared Library](https://www.google.com/search?q=%23-registering-shared-library)
  - [Using Shared Library](https://www.google.com/search?q=%23-using-shared-library)
  - [Global Variables](https://www.google.com/search?q=%23-global-variables)
  - [Groovy Classes](https://www.google.com/search?q=%23-groovy-classes)
  - [Parameters](https://www.google.com/search?q=%23-parameters)
  - [Library Resources](https://www.google.com/search?q=%23-library-resources)
  - [Declarative Pipelines](https://www.google.com/search?q=%23-declarative-pipelines)
  - [Tips & Best Practices](https://www.google.com/search?q=%23-tips--best-practices)
  - [Troubleshooting](https://www.google.com/search?q=%23-troubleshooting)
  - [Additional Resources](https://www.google.com/search?q=%23-additional-resources)
  - [Summary](https://www.google.com/search?q=%23-summary)

-----

## 🔍 What is Shared Library

**Jenkins Shared Library** is a feature in **Jenkins Pipeline** that allows you to store pipeline code in a separate repository from the projects that use it.

  * When you install Jenkins Pipeline, the Shared Library feature is **automatically available**.

-----

## ✨ Why Use Shared Library

### Benefits:

  * **Centralized Management** - No need to create pipelines manually for each project.
  * **Code Reusability** - Create a centralized repository containing reusable pipeline steps.
  * **Easy Maintenance** - Update pipeline code in one place, which is automatically applied to all projects.
  * **Consistency** - Ensure all projects use standardized pipeline configurations.

### Requirements:

  * Jenkins Shared Library requires an **SCM** (Source Control Management) system like **Git**.
  * Store your shared library in a **Git repository**.

-----

## 📦 Prerequisites

  * **Jenkins** installed with **Pipeline plugin** (usually default).
  * **Git repository** for storing shared library code.
  * Basic knowledge of **Groovy programming language**.
  * Access to **Jenkins configuration** (admin rights) to register the library globally.

-----

## 🚀 Creating Shared Library

Creating a Jenkins Shared Library involves these simple steps:

1.  **Create a folder** with the required structure.
2.  **Add Groovy code files** following the defined structure (`src/`, `vars/`).
3.  **Store the folder** in a Git repository.
4.  **Jenkins** will use Git to access and utilize the shared library code.

-----

## 📁 Folder Structure

Your shared library **must** follow this specific folder structure:

```
jenkins-shared-library/
├── resources/          # Non-code files (text, JSON, images, etc.)
├── src/               # Groovy class files (must be in package structure)
│   └── your/package/  # Example package structure
└── vars/              # Global variable Groovy files
```

### Directory Descriptions

  * **`resources/`** - Contains non-code files like text, JSON, images, and other assets.
  * **`src/`** - Contains **Groovy class files** organized in a **package structure**.
  * **`vars/`** - Contains Groovy files that automatically become **global variables** or custom steps in your pipeline.

-----

## 🔧 Registering Shared Library

Before using a shared library, you must register it in Jenkins.

1.  **Login to Jenkins** and access your Jenkins instance.
2.  **Navigate to Manage Jenkins** from the dashboard.
3.  **Open System Configuration** by selecting **Configure System**.
4.  **Locate Pipeline Libraries** by scrolling down to the **Global Trusted Pipeline Libraries** section.
5.  **Add New Library** by clicking the **Add** button.
6.  **Configure Library Settings:**
      * **Name:** Enter the library name (best practice: match the repository name).
      * **Default version:** Specify the default branch/tag (e.g., `main`).
7.  **Set Retrieval Method:**
      * Select **Modern SCM** as the retrieval method.
8.  **Configure Source Control:**
      * Select **Git** as the Source Code Management.
      * **Project Repository:** Enter the Git repository URL.
      * **Credentials:** Select or add your GitHub/SCM credentials.
9.  **Save Configuration** by clicking **Save** to complete registration.

-----

## 🎯 Using Shared Library

### Groovy Pipeline Plugin

  * Jenkins Shared Library requires the **Groovy Pipeline Plugin** (`pipeline-groovy-lib`), which is installed by default with Jenkins Pipeline.

### `@Library` Annotation

Use the `@Library` annotation to import your shared library at the top of your `Jenkinsfile`.

**Syntax:**

```groovy
@Library("library-name")
@Library("library-name@tag")
@Library("library-name@branch")
```

**Examples:**

```groovy
// Use default version configured in Jenkins
@Library('jenkins-shared-library')

// Use specific branch
@Library('jenkins-shared-library@main')

// Use specific tag
@Library('jenkins-shared-library@v1.0.0')
```

### Auto-Import `vars` Files

  * Add an underscore `_` after `@Library` to automatically import all files from the **`vars`** folder.

<!-- end list -->

```groovy
@Library('jenkins-shared-library@main') _
```

  * This makes all files in `vars/` available as global variables/steps without explicit imports.

-----

## 🌐 Global Variables

Files in the `vars/` folder automatically become global variables accessible in your `Jenkinsfile`.

### How It Works

  * **File name = Variable name**
      * `author.groovy` → `author` variable
      * `hello.groovy` → `hello` variable
  * All functions, variables, and classes defined inside the file are accessible through this global variable.

### Defining Functions (vars/hello.groovy)

```groovy
def world() {
  echo("Hello World")
}
```

**Jenkinsfile Usage:**

```groovy
@Library('jenkins-shared-library@main') _

pipeline {
  agent any
  stages {
    stage("Hello World") {
      steps {
        script {
          hello.world() // Calls the world() function from hello.groovy
        }
      }
    }
  }
}
```

### Default Function (`call`)

  * The special **`call()`** function is invoked when you use the global variable *as a function itself* without specifying a method.

**vars/author.groovy:**

```groovy
def call() {
  "Junior Full Stack Developer"
}

def name() {
  "Dzaru Rizky Fathan Fortuna"
}
```

**Jenkinsfile Usage:**

```groovy
@Library('jenkins-shared-library@main') _

pipeline {
  // ...
  steps {
    script {
      echo(author())           // Invokes the call() function
      echo(author.name())      // Invokes the name() function
    }
  }
}
```

-----

## 🎓 Groovy Classes

You can create more structured code by using **Groovy classes** in the `src/` directory.

### Creating Groovy Classes (src/dzarurizkyy/jenkins/Output.groovy)

```groovy
package dzarurizkyy.jenkins

class Output {
  static def hello(String name) {
    println("Hello ${name}")
  }
}
```

### Using Groovy Classes (Jenkinsfile)

  * You must use the standard **`import`** statement.

<!-- end list -->

```groovy
@Library('jenkins-shared-library@main') _

import dzarurizkyy.jenkins.Output // Import the Groovy Class

pipeline {
  agent any
  stages {
    stage("Login") {
      steps {
        script {
          Output.hello("Dzaru Rizky") // Use the static method
        }
      }
    }
  }
}
```

### Accessing Jenkins Pipeline Steps in Classes

  * Standard Groovy classes (`src/`) do **not** automatically have access to Jenkins Pipeline steps (like `sh`, `echo`, `git`).
  * To use them, you must **pass the `steps` object** (which is `this` inside a `script` block) as a parameter.

**src/dzarurizkyy/jenkins/Output.groovy:**

```groovy
package dzarurizkyy.jenkins

class Output {
  static def hello(steps, String name) { // Accept the steps object
    steps.echo("Hello ${name}") // Use the echo step via the steps object
  }
}
```

**Jenkinsfile Usage:**

```groovy
// ...
steps {
  script {
    Output.hello(this, "Dzaru Rizky") // Pass 'this' (the steps object)
  }
}
// ...
```

### Groovy vs. Pipeline Functions

  * Use **`echo()`** for output in Jenkins Pipeline/Shared Library code (can be seen in console output).
  * Use **`println()`** for output in pure Groovy class code (`src/`) (only seen in log if running in script).

-----

## 📊 Parameters

Functions in shared libraries can accept **parameters** just like standard Groovy functions.

### String Parameters

**vars/command.groovy:**

```groovy
def call(String command) {
  sh("echo 'this is ${command}' > command.txt")
}
```

**Jenkinsfile Usage:**

```groovy
// ...
steps {
  script {
    command("function call") // Passing a String parameter
  }
}
// ...
```

### List Parameters

  * Use `List` parameters for variable-length or array-like arguments.

**vars/command.groovy:**

```groovy
def call(List commands) {
  for (command in commands) {
    sh("echo ${command}")
  }
}
```

**Jenkinsfile Usage:**

```groovy
// ...
steps {
  script {
    command(["Test 1", "Test 2", "Test 3"]) // Passing a Groovy List
  }
}
// ...
```

### Map Parameters

  * `Map` parameters are ideal for complex, key-value data structures.

**vars/hello.groovy:**

```groovy
def person(Map person) {
  echo("Hello ${person.firstName} ${person.lastName}")
}
```

**Jenkinsfile Usage:**

```groovy
// ...
steps {
  script {
    hello.person([
      "firstName": "Rakuro",
      "lastName": "Hizutome"
    ]) // Passing a Groovy Map
  }
}
// ...
```

-----

## 📦 Library Resources

The **`resources/`** folder stores non-code files (text, JSON, images, configuration files, etc.).

### Accessing Resources

  * Use the built-in **`libraryResource(location)`** function to retrieve files.
  * The function returns the file content as a **text string**.

**Syntax:**

```groovy
def fileContent = libraryResource("path/to/file/from/resources")
```

**Example (resources/config/build.json):**

```json
{
  "app": "Jenkins Shared Library",
  "author": "Dzaru Rizky Fathan Fortuna"
}
```

**Jenkinsfile Usage:**

```groovy
// ...
steps {
  script {
    def config = libraryResource("config/build.json") // Relative path from resources/
    echo(config)
  }
}
// ...
```

-----

## 🔄 Declarative Pipelines

Shared libraries support creating complete declarative pipelines, which can significantly simplify your main `Jenkinsfile`.

### Basic Declarative Pipeline Example

**vars/declarative.groovy:**

```groovy
def call(Map setup) {
  pipeline { // Start the entire pipeline block
    agent any
    stages {
      stage("Example Stage") {
        steps {
          script {
            // Can call other global variables
            hello.person([
              "firstName": "Rakuro",
              "lastName": "Hizutome"
            ])
            // Can use library resources
            def config = libraryResource("config/build.json")
            echo(config)
          }
        }
      }
      // ... more stages
    }
  }
}
```

**Jenkinsfile Usage:**

```groovy
@Library('jenkins-shared-library@main') _

// Call the declarative step like a function
declarative([:]) // Pass empty Map or configuration parameters
```

### Conditional Pipelines

  * You can add **Groovy logic** (like `if/else`) inside the `call()` function of a global variable (`vars/`) to determine *which* pipeline structure to execute.

**vars/declarative.groovy (Conditional Logic):**

```groovy
def call(Map setup) {
  if (setup.type == "start") {
    // Pipeline configuration A
    pipeline {
      // ... stages for 'start' type
    }
  } else {
    // Pipeline configuration B
    pipeline {
      // ... stages for other types
    }
  }
}
```

**Jenkinsfile Usage:**

```groovy
@Library('jenkins-shared-library@main') _

// Will execute Pipeline Configuration A
declarative([type: "start"])
```

-----

## 💡 Tips & Best Practices

### Naming Conventions

  * Use **descriptive names** for files and functions.
  * Follow **Java/Groovy package naming conventions** for `src/` (e.g., `com.company.project`).
  * Use **lowercase with hyphens** for repository names (e.g., `jenkins-shared-library`).

### Code Organization

  * Keep `vars/` files **simple and focused** on pipeline steps/flows.
  * Use **`src/`** for **complex logic, utilities, and reusable classes** that are not directly tied to Jenkins steps.
  * Store configuration files, templates, and static data in **`resources/`**.

### Version Control

  * Use **semantic versioning** for tags (e.g., `v1.0.0`, `v2.0.0`).
  * Create **branches** for major development or breaking changes.
  * Ensure the branch/tag used in `@Library` is **stable** (e.g., use a tag or a stable `main` branch).

-----

## 🆘 Troubleshooting

### Library Not Found

  * Verify the library is **registered in Jenkins** (check name and SCM URL).
  * Check the library **name matches** exactly in the `@Library` annotation.
  * Ensure the **Git repository is accessible** and the specified branch/tag exists.

### Import Errors (for src/ classes)

  * Verify the **package structure** in `src/` matches the `import` statement in the `Jenkinsfile`.
  * Check that **class names match file names**.

### Global Function Not Available (for vars/ files)

  * Verify the `.groovy` file exists in the **`vars/`** folder.
  * Ensure you have the **`_`** (underscore) added after the `@Library` annotation for auto-import.
  * Check for syntax errors in the Groovy file.

-----

## 📚 Additional Resources

  * [**Jenkins Pipeline Documentation**](https://www.jenkins.io/doc/book/pipeline/)
  * [**Jenkins Shared Libraries Official Guide**](https://www.jenkins.io/doc/book/pipeline/shared-libraries/)
  * [**Groovy Language Documentation**](https://groovy-lang.org/documentation.html)

-----

## ✅ Summary

Jenkins Shared Library provides a powerful way to:

  * **Centralize** pipeline code across multiple projects.
  * **Reduce code duplication** and maintenance overhead.
  * **Standardize** build and deployment processes.
  * Create **reusable, modular** pipeline components.
  * Improve team collaboration and consistency.