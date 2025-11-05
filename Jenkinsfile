pipeline {
    agent {
      node {
        label "linux && java17"
      }
    }
    stages {
        stage("Build") {
            steps {
              echo "Test Build 1"
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
