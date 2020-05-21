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
    stage ('Source Composition Analysis') {
      steps {
         sh 'rm owasp* || true'
         sh 'wget "https://raw.githubusercontent.com/iis-sec/webapp/master/owasp-dependency-check.sh" '
         sh 'chmod +x owasp-dependency-check.sh'
         sh 'bash owasp-dependency-check.sh'
         sh 'cat /root/OWASP-Dependency-Check/reports/dependency-check-report.xml'
        
      }
    }  
    stage ('Build') {
      steps {
      sh 'mvn clean package'
       }
    }
    
    stage ('Deploy-To-Tomcat') {
            steps {
           sshagent(['tomcat']) {
                sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@35.154.248.64:/root/jenkins_workspace/tomcat/apache-tomcat-8.5.55/webapps/webapp.war'
              }      
           }       
    }    
    
    stage ('DAST') {
      steps {
        sshagent(['tomcat']) {
         sh 'ssh -o  StrictHostKeyChecking=no ubuntu@35.154.248.64 "docker run -t owasp/zap2docker-stable zap-baseline.py -t http://35.154.248.64:8087/webapp/" || true'
        }
      }
    }
    
    
  }
}
