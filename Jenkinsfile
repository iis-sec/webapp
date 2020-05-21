pipeline {
  agent { label 'ubuntu' }
  tools {
    maven 'Maven'
  }
  
  stages {
    stage ('Initialize') {
      steps {
        sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
            ''' 
      }
    }
   stage ('Check-Git-Secrets') {
      steps {
        sh 'rm trufflehog || true'
        sh 'docker run gesellix/trufflehog --json https://github.com/iis-sec/webapp.git > trufflehog'
        sh 'cat trufflehog'
      }
    }            
    stage ('Build') {
      steps {
      sh 'mvn clean package'
       }
    }
    

    
    
    
    
  }
}
