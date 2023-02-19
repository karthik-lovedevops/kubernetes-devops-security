pipeline {
  agent any

  stages {
      stage('Build Artifact using github') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar' 
            }
        }   
    }
}
