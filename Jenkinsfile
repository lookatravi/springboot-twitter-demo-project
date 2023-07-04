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
        		
		
		stage("Jar Publish") {
            steps {
                script {
                        echo '<--------------- Jar Publish Started --------------->'
                         def server = Artifactory.newServer url:registry+"/artifactory" ,  credentialsId:"artifactorycredentialid"
                         def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}";
                         def uploadSpec = """{
                              "files": [
                                {
                                  "pattern": "jarstaging/(*)",
                                  "target": "ravi-libs-release/{1}",
                                  "flat": "false",
                                  "props" : "${properties}",
                                  "exclusions": [ "*.sha1", "*.md5"]
                                }
                             ]
                         }"""
                         def buildInfo = server.upload(uploadSpec)
                         buildInfo.env.collect()
                         server.publishBuildInfo(buildInfo)
                         echo '<--------------- Jar Publish Ended --------------->'  
                
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
		 
		
		stage (" Docker Publish "){
            steps {
                script {
                   echo '<--------------- Docker Publish Started --------------->'  
                    docker.withRegistry(registry, 'dockercredentialid'){
                        app.push()
                    }    
                   echo '<--------------- Docker Publish Ended --------------->'  
                }
            }
        }
		
		
                       
    }
 }

