pipeline {
    agent {
      node {
        label "linux && java17"
      }
    }
    stages {
        stage("Build") {
            steps {
              echo "Build"
            }
        }
        stage("Test") {
          steps {
            echo "Test"
            sh("error test")
          }
        }
        stage("Deploy") {
          steps {
            echo "Deploy"
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
