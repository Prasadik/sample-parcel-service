pipeline {
  agent {label 'slave1'}
    parameters {
      string(name:'cmd1',description:'give build the command',defaultValue:'clean')
      string(name:'cmd2',description:'give build the command',defaultValue:'install')
      //string(name:'cmd3',description:'give build the command',defaultValue:'spring-boot')
      //string(name:'cmd4',description:'give build the command',defaultValue:'run')
       // choice(choices:['package','compile','install'],name:'cmd2')
                }
    stages {
      stage('checkout') {
        steps {
          sh "rm -rf sample-parcel-service"
          sh "git clone https://github.com/Prasadik/sample-parcel-service.git"
          sh "cd sample-parcel-service"
              }      
            }
      stage('SetupEnvironment') {
        steps {
            sh 'export export JAVA_HOME=$(dirname $(dirname $(readlink -f $(which java))))'            
	        sh 'export MAVEN_HOME=/usr/share/maven'           
        }
    }
        stage('build') {
        steps {
          sh "mvn $cmd1 $cmd2"
              }      
            }

	 /*   stage('Upload Artifact') {
            steps {
                echo 'Uploading artifact...'
                archiveArtifacts artifacts: 'target/simple-parcel-service-app-1.0-SNAPSHOT.jar', allowEmptyArchive: true
            }
        } */
        stage('deploy') {
        steps {
          sh "mvn spring-boot:run"
	  sh "java -jar target/simple-parcel-service-app-1.0-SNAPSHOT.jar"
              }      
            }
          }
      }
