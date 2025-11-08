pipeline {
    agent none
    options {
      disableConcurrentBuilds()
      timeout(time: 10, unit: "MINUTES")
    }
    stages {
        stage("Prepare") {
          agent {
            node {
              label "linux && java17"
            }
          }
          steps {
            echo("Start Job: ${env.JOB_NAME}")
            echo("Start Build: ${env.BUILD_NUMBER}")
            echo("Branch Name: ${env.BRANCH_NAME}")
            sleep(5)
          }
        }
        stage("Build") {
            agent {
              node {
                label "linux && java17"
              }
            }
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
          agent {
            node {
              label "linux && java17"
            }
          }
          steps {
            echo "Test 1"
            sleep(5)
          }
        }
        stage("Deploy") {
          agent {
            node {
              label "linux && java17"
            }
          }
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
