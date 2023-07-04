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
                //sh 'mvn surefire-report:report'
                echo '<------------- Unit Testing stopped  --------------->'
            }
        }
         
		stage ("Sonar Analysis") {
            environment {
               scannerHome = tool 'sonarQubescanner'
            }
            steps {
                echo '<--------------- Sonar Analysis started  --------------->'
                withSonarQubeEnv('SonarServer') {
                    sh "${scannerHome}/bin/sonar-scanner"
                }    
                echo '<--------------- Sonar Analysis stopped  --------------->'
            }   
        }    
        
		stage ("Quality Gate") {

            steps {
                script {
                  echo '<--------------- Quality Gate started  --------------->' 
                    timeout(time: 1, unit: 'HOURS') {
                        def qg = waitForQualityGate()
                        if(qg.status!='OK'){
                          error "Pipeline failed due to the Quality gate issue"   
                        }    
                    }    
                  echo '<--------------- Quality Gate stopped  --------------->'
                }    
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

