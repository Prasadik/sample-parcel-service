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
          sh "rm -rf petclicnicnew"
          sh "git clone https://github.com/Prasadik/petclicnicnew.git"
              }      
            }
        stage('build') {
        steps {
          sh "cd petclicnicnew"
          sh "mvn $cmd1 $cmd2"
              }      
            }
        stage('deploy') {
        steps {
          sh "mvn spring-boot:run"
              }      
            }
          }
      }
