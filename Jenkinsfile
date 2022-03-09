pipeline {
   agent any
   tools {
      maven 'Maven'
   }
   stages {
       stage("Build") {
                steps {
                    echo "Building" 
                    sh 'mvn -X clean install -DskipTests'
                }
       }
        stage("Test") {
           steps {
               echo "Testing"
               sh 'mvn test'
           }
          post {
                always {
                    junit '**/target/surefire-reports/*.xml' 
                }
          }
        }
      
      stage("Create Artifact for prod") {
           steps {
              echo "Creating artifact"
              sh 'mvn package'
              sleep 3
              snDevOpsArtifact(artifactsPayload:"""
               {"artifacts": 
                  [
                     {
                        "name": "avgbrewingapp-mvp3.jar",
                        "version":"0.${env.BUILD_NUMBER}.0",
                        "semanticVersion": "0.${env.BUILD_NUMBER}.0",
                        "repositoryName": "bm-artifacts-repo"
                       }
                    ]
                 }""")
                 
                
           }
        }
  
      stage("Deploy") {
             steps{ 
                 snDevOpsPackage(name: "avgbrewingapp", artifactsPayload: """
              {"artifacts": 
               [
                  {
                     "name": "avgbrewingapp-mvp3.jar",
                     "repositoryName": "bm-artifacts-repo",
                     "pipelineName": "Average App Pipeline",
                     "taskExecutionNumber":"${env.BUILD_NUMBER}",
                     "stageName":"Create Artifact for prod",
                     "branchName": "master"
                   }
                 ]
                }""")
                
              snDevOpsChange()
                  echo ">> Deploy in prod"
              }
      }      
      
  }
}
