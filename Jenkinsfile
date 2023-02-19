pipeline {
  agent any

  stages {
      stage('Build Artifact using github') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar' 
            }
        }   
      
      stage('Unit test') {
            steps {
              sh "mvn test"
            }
        } 
    }
}
