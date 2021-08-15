pipeline {
   agent any
   environment {
      PATH ="/usr/local/apache-maven/bin:$PATH" 
     }
     stages {
        stage ("cloning git repo") {
            steps {
               git branch: 'master', url: 'https://github.com/anilp2005/Bankinfo_main.git'    
            }          
        }
        stage ("building & testing the code with sonar") {
            steps {
    withSonarQubeEnv('SonarQube_token') {
      sh "${mvn}/bin/mvn sonar:sonar"
            }          
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
                    url: 'http://192.168.50.130:8082',
                    username: 'admin',
                    password: 'Admin@2021',
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
                  "target": "bankinfo_main-libs-snapshot-local/"
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
              sh  "git clone https://github.com/anilp2005/Bankinfo_main.git"
              sh  "cd BankInfo-1"
              sh  "cp /var/lib/jenkins/workspace/multibranch_pipeline_master/target/BankInfo-0.0.1-SNAPSHOT.jar ."
              sh  "sudo docker build -t bankinfoimage ." 
              sh  "sudo docker run -itd --name bankinfocontainer -p 3333:8989 bankinfoimage"
             }
           }
        }
    }
