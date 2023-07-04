def imageName = 'ravidevops1507.jfrog.io/ravidevops-docker/ravidevops-rtp'
def registry  = 'https://ravidevops1507.jfrog.io'
def version   = '1.0.2'
def app
pipeline {
    agent {
       node {
         label "master"
      }
    }
    stages {
        stage('Build') {
            steps {
                echo '<--------------- Building --------------->'
                sh 'printenv'
                sh 'mvn clean deploy -Dmaven.test.skip=true'
                echo '<------------- Build completed --------------->'
            }
        }
        stage('Unit Test') {
            steps {
                echo '<--------------- Unit Testing started  --------------->'
                #sh 'mvn surefire-report:report'
                echo '<------------- Unit Testing stopped  --------------->'
            }
        }
         
		stage(" Docker Build ") {
          steps {
            script {
               echo '<--------------- Docker Build Started --------------->'
               app = docker.build(imageName+":"+version)
               echo '<--------------- Docker Build Ends --------------->'
            }
          }
        }
		 
                       
    }
 }

