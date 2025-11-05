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
              echo "Test Build 2"
              echo "Test Build 3"
            }
        }
        stage("Test") {
          steps {
            echo "Test 1"
            echo "Test 2"
            echo "Test 3"
          }
        }
        stage("Deploy") {
          steps {
            echo "Deploy 1"
            echo "Deploy 2"
            echo "Deploy 3"
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
