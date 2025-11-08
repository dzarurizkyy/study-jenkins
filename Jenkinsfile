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
    stages {
        stage("Prepare") {

          environment {
            APP = credentials("credential-test")
          }

          steps {
            echo("AUTHOR ${author}")
            echo("EMAIL ${email}")
            echo("LINKEDIN ${linkedin}")
            
            echo("APP USER: ${APP_USR}")
            sh("echo 'APP_PASSWORD: ${APP_PSW}' > 'rahasia.txt'")

            echo("Start Job: ${env.JOB_NAME}")
            echo("Start Build: ${env.BUILD_NUMBER}")
            echo("Branch Name: ${env.BRANCH_NAME}")
            sleep(5)
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
          steps {
            echo "Deploy 1"
            sleep(5)
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
