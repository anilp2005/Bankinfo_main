pipeline {
   agent any
   environment {
      PATH ="/usr/share/maven/bin:$PATH" 
     }
     stages {
        stage ("cloning git repo") {
            steps {
               git branch: 'develop', url: 'https://github.com/gowthamvishnu/BankInfo-1.git'    
            }          
        }
        stage ("building & testing the code with sonar") {
            steps {
               sh "mvn clean install sonar:sonar" 
            }          
        }
        stage ('Code Quality') {
            steps {
                jacoco()
            }
        }
        stage ('Jfrog server') {
            steps {
                rtServer (
                    id: 'Artifactory',
                    url: 'http://192.168.50.170:8082/artifactory',
                    username: 'admin',
                    password: 'password',
                    bypassProxy: true,
                    timeout: 300
                )
            }
        }
        stage ('uploading arctifacts') {
            steps {
                rtUpload (
                  serverId: 'Artifactory',
                  spec: '''{
                  "files": [
                   {
                  "pattern": "*.jar",
                  "target": "vishnurepo-libs-snapshot-local/"
                }
                 ]
                }''',
                 )
                }
            }
          stage ("building docker container") {
            steps {
              sh  "rm -rf /var/lib/jenkins/workspace/multibranch_pipeline_master/BankInfo-1"
              sh  "docker rm -f bankinfocontainer"
              sh  "docker rmi -f bankinfoimage"
              sh  "git clone https://github.com/gowthamvishnu/BankInfo-1.git"
              sh  "cd BankInfo-1"
              sh  "cp /var/lib/jenkins/workspace/multibranch_pipeline_master/target/BankInfo-0.0.1-SNAPSHOT.jar ."
              sh  "sudo docker build -t bankinfoimage ." 
              sh  "sudo docker run -itd --name bankinfocontainer -p 3333:8989 bankinfoimage"
             }
           }
        }
    }
