pipeline {
    agent {
      node {
        label "linux && java17"
      }
    }

    options {
      disableConcurrentBuilds()
      timeout(time: 10, unit: "MINUTES")
    }

    environment {
      AUTHOR = "Dzaru Rizky Fathan Fortuna"
      EMAIL = "dzarurizkybusiness@gmail.com"
      LINKEDIN = "https://www.linkedin.com/in/dzarurizky"
    }

    parameters {
      string(name: "NAME", defaultValue: "Guest", description: "What is your name?")
      text(name: "DESCRIPTION", defaultValue: "Guest", description: "Tell me about you?")
      booleanParam(name: "DEPLOY", defaultValue: false, description: "Need to Deploy?")
      choice(name: "SOCIAL_MEDIA", choices: ["Instagram", "Facebook", "Twitter"], description: "Which Social Media?")
      password(name: "SECRET", defaultValue: "", description: "Encrypt key")
    }

    triggers {
      pollSCM("*/5 * * * *")
    }

    stages {
        stage("OS Setup") {

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
              stage("Setup") {
                steps {
                  echo("Setup ${OS} ${ARC}")
                }
              }
            }
          }
        }
        stage("Prepare") {

          environment {
            APP = credentials("credential-test")
          }

          parallel {
            stage("Global Variable") {
              steps {
                echo("Start Job: ${env.JOB_NAME}")
                echo("Start Build: ${env.BUILD_NUMBER}")
                echo("Branch Name: ${env.BRANCH_NAME}")
              }
            }

            stage("Enviroment Variable") {
              steps {
                echo("AUTHOR ${author}")
                echo("EMAIL ${email}")
                echo("LINKEDIN ${linkedin}")

                echo("APP USER: ${APP_USR}")
                sh('echo "APP_PASSWORD: $APP_PSW" > "secret.txt"')
              }
            }
          }
        }
        stage("Parameter") {
          steps {
            echo "Hello ${params.NAME}!"
            echo "You description is ${params.DESCRIPTION}!"
            echo "Your social media is ${params.SOCIAL_MEDIA}"
            echo "Need to deploy ${params.DEPLOY}"
            echo "Your secret is ${params.SECRET}"
          }
        }
        stage("Build") {
            steps {
              echo "Test Build 1"
              sh("cat note.txt")
              script {
                for (int i = 1; i <= 10; i++) {
                  echo("Script ${i}")
                }
                def data = [
                  "role": "Web Programmer",
                  "position": "Junior"
                ]
                writeJSON(file: "data.json", json: data)
              }
              sleep(5)
            }
        }
        stage("Test") {
          steps {
            echo "Test 1"
            sleep(5)
          }
        }
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
            echo "Deploy to ${TARGET_ENV}"
            sleep(5)
          }
        }
        stage("Release") {
          when {
            expression {
              return params.DEPLOY
            }
          }
          steps {
            withCredentials([usernamePassword(
              credentialsId: "credential-test",
              usernameVariable: "USER",
              passwordVariable: "PASSWORD"
            )])
            sh('echo "Release it with -u $USER -p $PASSWORD" > "release.txt"')
          }
        }
    }
    post {
      always {
        echo "I will always say Hello Again!"
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

