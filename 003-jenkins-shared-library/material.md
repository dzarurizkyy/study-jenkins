# 📚 Jenkins Shared Library Guide

A comprehensive guide for creating and using Jenkins Shared Libraries to centralize and reuse pipeline code across multiple projects.

---

## 📋 Table of Contents

- [What is Shared Library](#-what-is-shared-library)
- [Why Use Shared Library](#-why-use-shared-library)
- [Prerequisites](#-prerequisites)
- [Creating Shared Library](#-creating-shared-library)
- [Folder Structure](#-folder-structure)
- [Registering Shared Library](#-registering-shared-library)
- [Using Shared Library](#-using-shared-library)
- [Global Variables](#-global-variables)
- [Groovy Classes](#-groovy-classes)
- [Parameters](#-parameters)
- [Library Resources](#-library-resources)
- [Declarative Pipelines](#-declarative-pipelines)

---

## 🔍 What is Shared Library

Jenkins Shared Library is a feature in Jenkins Pipeline that allows you to store pipeline code in a separate repository from the projects that use it.

---

## ✨ Why Use Shared Library

**Benefits:**

- **Centralized Management** - No need to create pipelines manually for each project
- **Code Reusability** - Create a centralized repository containing pipelines
- **Easy Maintenance** - Update pipeline code in one place, applied to all projects
- **Consistency** - Ensure all projects use standardized pipeline configurations

**Requirements:**

- Jenkins Shared Library requires an SCM (Source Control Management) system like Git
- Store your shared library in a Git repository

---

## 📦 Prerequisites

- Jenkins installed with Pipeline plugin
- Git repository for storing shared library
- Basic knowledge of Groovy programming language
- Access to Jenkins configuration (admin rights)

---

## 🚀 Creating Shared Library

Creating a Jenkins Shared Library is straightforward:

- Create a folder with the required structure
- Add Groovy code files following the defined structure
- Store the folder in a Git repository
- Jenkins will use Git to access and utilize the shared library

---

## 📁 Folder Structure

Your shared library must follow this specific folder structure:

```
jenkins-shared-library/
├── resources/          # Non-code files (text, JSON, images, etc.)
├── src/               # Groovy class files
│   └── your/package/  # Package structure
└── vars/              # Global variable Groovy files
```

**Directory Descriptions**

- **resources/** - Contains non-code files like text, JSON, images, and other assets
- **src/** - Contains Groovy class files organized in package structure
- **vars/** - Contains Groovy files that become global variables

---

## 🔧 Registering Shared Library

Before using a shared library, you must register it in Jenkins:

- **Login to Jenkins**
   
   Access your Jenkins instance

- **Navigate to Manage Jenkins**
   
   Click on **Manage Jenkins** from the dashboard

- **Open System Configuration**
   
   Select **Configure System**

- **Locate Pipeline Libraries**
   
   Scroll down to **Global Trusted Pipeline Libraries** section

- **Add New Library**
   
   Click **Add** button

- **Configure Library Settings**
   
   - **Name:** Enter library name (should match repository name - best practice)
   - **Default version:** Specify branch/tag (e.g., `main`)

- **Set Retrieval Method**
   
   - Select **Modern SCM** as retrieval method

- **Configure Source Control**
   
   - Select **Git** as Source Code Management
   - **Project Repository:** Enter Git repository URL
   - **Credentials:** Select or add GitHub credentials

- **Save Configuration**
   
   Click **Save** to complete registration

---

## 🎯 Using Shared Library

### Groovy Pipeline Plugin

Jenkins Shared Library requires the Groovy Pipeline Plugin, which is installed by default with Jenkins Pipeline.

- **Plugin Information:** https://plugins.jenkins.io/pipeline-groovy-lib/

- **@Library Annotation**

   Use the `@Library` annotation to import your shared library:
   
   **Syntax:**
   
   ```groovy
   @Library("library-name")
   @Library("library-name@tag")
   @Library("library-name@branch")
   ```

  **Examples:**

   ```groovy
   // Use default version
   @Library('jenkins-shared-library')
   
   // Use specific branch
   @Library('jenkins-shared-library@main')
   
   // Use specific tag
   @Library('jenkins-shared-library@v1.0.0')
   ```

- **Auto-Import vars Files**

   Add an underscore `_` after `@Library` to automatically import all files from the `vars` folder:
   
   ```groovy
   @Library('jenkins-shared-library@main') _
   ```
   
   This makes all files in `vars/` available as global variables without explicit imports.

---

## 🌐 Global Variables

Files in the `vars/` folder automatically become global variables accessible in your Jenkinsfile.

- **How It Works**

   - **File name = Variable name**
   
     `author.groovy` → `author` variable
     
     `hello.groovy` → `hello` variable
     
   - All functions, variables, and classes in the file are accessible through the global variable

- **Defining Functions**

   `vars/hello.groovy`
   
   ```groovy
   def world() {
     echo("Hello World")
   }
   ```

   
   `Jenkinsfile Usage`
   
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

- **Multiple Functions**

   `vars/author.groovy`
   
   ```groovy
   def name() {
     "Dzaru Rizky Fathan Fortuna"
   }
   
   def channel() {
     "dzarurizky"
   }
   ```

   
   `Jenkinsfile Usage`
   
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

- **Default Function (call)**

   The `call()` function is invoked when you use the global variable as a function:

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

   
   `Jenkinsfile Usage`
   
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
---

## 🎓 Groovy Classes

You can create Groovy classes in the `src/` directory for more structured code.

- **Creating Groovy Classes**

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

- **Groovy vs Jenkins Pipeline**

   - **Jenkins Pipeline:** Use `echo()` function
   - **Groovy:** Use `println()` function

- **Accessing Jenkins Pipeline Steps in Classes**

   To use Jenkins Pipeline steps in Groovy classes, pass the `steps` object:
   
   `src/dzarurizkyy/jenkins/Output.groovy`
   
   ```groovy
   package dzarurizkyy.jenkins
   
   class Output {
     static def hello(steps, String name) {
       steps.echo("Hello ${name}")
     }
   }
   ```
   
   `Jenkinsfile Usage`
   
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

- **Why Use Global Variables Over Classes?**

   Global variables in `vars/` can directly access Jenkins Pipeline Library functions, making them more convenient for pipeline-related scripts.
   
   **Recommendation:** Use global variables (`vars/`) when you need Jenkins Pipeline Library features.

---

## 📊 Parameters

Functions in shared libraries can accept parameters just like standard Groovy functions.

- **String Parameters**

   `vars/command.groovy`
   
   ```groovy
   def call(String command) {
     sh("echo 'this is ${command}' > command.txt")
   }
   ```
   
   `Jenkinsfile Usage`
   
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

- **List Parameters**

   Use List parameters for variable-length arguments:
   
   `vars/command.groovy`
   
   ```groovy
   def call(List commands) {
     for (command in commands) {
       sh("echo ${command}")
     }
   }
   ```
   
   `Jenkinsfile Usage`
   
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

- **Map Parameters**

   Map parameters are ideal for complex, key-value data:
   
   `vars/hello.groovy`
   
   ```groovy
   def person(Map person) {
     echo("Hello ${person.firstName} ${person.lastName}")
   }
   ```
   
   `Jenkinsfile Usage`
   
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

---

## 📦 Library Resources

The `resources/` folder stores non-code files like text, JSON, images, and other assets.

- **Accessing Resources**

   Use the `libraryResource(location)` function to retrieve files from the resources folder:

   - **Syntax:**
   
      ```groovy
      libraryResource("path/to/file")
      ```
   
      The function returns the file content as text.
   
   - **Example**
   
      `resources/config/build.json`
      
      ```json
      {
        "app": "Jenkins Shared Library",
        "author": "Dzaru Rizky Fathan Fortuna"
      }
      ```
   
      `Jenkinsfile Usage`
      
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
   
      `Output:`
      
      ```
      {
        "app": "Jenkins Shared Library",
        "author": "Dzaru Rizky Fathan Fortuna"
      }
      ```

---

## 🔄 Declarative Pipelines

Shared libraries support creating complete declarative pipelines, simplifying Jenkinsfiles significantly.

- **Basic Example**

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

  `Jenkinsfile Usage`

   ```groovy
   @Library('jenkins-shared-library@main') _
   
   declarative([:])
   ```

- **Conditional Pipelines**

  Add logic to declarative pipelines using conditions:
   
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
   
   `Jenkinsfile Usage`
   
   ```groovy
   @Library('jenkins-shared-library@main') _
   
   // Use "start" type
   declarative([type: "start"])
   
   // Or use different type
   declarative([type: "stop"])
   ```

---

## 💡 Tips & Best Practices

### Naming Conventions

- Use descriptive names for files and functions
- Follow Java/Groovy package naming conventions for `src/`
- Use lowercase with hyphens for repository names

### Code Organization

- Keep `vars/` files simple and focused
- Use `src/` for complex logic and reusable classes
- Store configuration files in `resources/`

### Version Control

- Use semantic versioning for tags (v1.0.0, v2.0.0)
- Create branches for major changes
- Use `main` or `master` for stable code

### Documentation

- Add comments to complex functions
- Document parameters and return values
- Maintain a CHANGELOG.md file

### Testing

- Test shared library changes before tagging
- Use feature branches for development
- Validate in non-production environments first

---

## 🔍 Example: Complete Shared Library Structure

```
jenkins-shared-library/
├── resources/
│   └── config/
│       └── build.json
├── src/
│   └── com/
│       └── company/
│           └── jenkins/
│               └── Output.groovy
└── vars/
    ├── author.groovy
    ├── command.groovy
    ├── declarative.groovy
    └── hello.groovy
```

---

## 🆘 Troubleshooting

### Library Not Found

- Verify library is registered in Jenkins
- Check library name matches in `@Library` annotation
- Ensure Git repository is accessible

### Import Errors

- Verify package structure in `src/`
- Check import statements match package names
- Ensure class names match file names

### Function Not Available

- Verify file exists in `vars/` folder
- Check for syntax errors in Groovy files
- Ensure `_` is added after `@Library` for auto-import

### Permission Issues

- Verify Jenkins has read access to Git repository
- Check credentials are properly configured
- Ensure branch/tag exists in repository
