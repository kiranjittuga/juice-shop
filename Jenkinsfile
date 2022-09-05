pipeline {
  agent any 
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
    
    stage ('Check-Git-Secrets'){
      steps {
        sh 'rm trufflehog || true'
        sh 'docker run gesellix/trufflehog  > trufflehog'
        sh 'cat trufflehog'
      }
    } 
    
    stage ('OWASP Dependency-Check Vulnerabilities') {
            steps {
                dependencyCheck additionalArguments: ''' 
                    -o "./" 
                    -s "./"
                    -f "ALL" 
                    --prettyPrint''', odcInstallation: 'OWASP-DC'

                dependencyCheckPublisher pattern: 'dependency-check-report.xml'
            }
    }
    
    stage ('SAST') {
      steps {
        withSonarQubeEnv('Sonar') {
          sh 'mvn sonar:sonar'
          sh 'cat target/sonar/report-task.txt'
        }
      }
    } 
    
   
    stage ('Build') {
      steps {
       sh 'mvn clean package'
      }
      
    }
  }
} 
