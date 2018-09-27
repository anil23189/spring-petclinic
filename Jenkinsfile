#!groovy
  def app
pipeline {

 agent any 
  tools {
     maven 'maven3'
  }
  
  stages {
    
     stage('Checkout'){    
       steps {
          checkout([$class: 'GitSCM', branches: [[name: '*/poc5']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '2b270278-6842-45b3-b483-8a223069238d', url: 'https://github.com/anil23189/spring-petclinic.git']]])              
       }
     }
     stage ('Java Build') {
       steps {
         //sh 'mvn clean install -DskipTests'
         sh 'mvn clean package -U'
       }
     }
     stage('Sonar') {
            steps {
               sh "whoami"
               //sh "mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.3.0.603:sonar -Dsonar.host.url=http://sonar-devel.local"
            }
        }  
    
    stage('Create Docker Image') {
     steps{
      //sh "sudo docker stop devops-poc/pipeline:latest" 
      //sh "sudo docker rm devops-poc/pipeline:latest" 
      sh "sudo docker build -t devops-poc/pipeline:latest ."
          }
     }
 
    stage('Run Docker Image') { 
                       
     steps{
      
         //sh "sudo docker run -p8082:8080  devops-poc-${env.VERSION_NUMBER}/pipeline:latest"     
         sh """
                    sudo docker run -d \
                        -p8082:8080 \
                        -v /var/run/docker.sock:/var/run/docker.sock:ro \
                        -e TIMEOUT=30 \
                        devops-poc/pipeline:latest
            """                        
           } 
       
      }
    stage('Run Smoke Tests') {
      steps {
        node('master') {
              build job: 'smoketest'
            }
      }
    }

  }
}
