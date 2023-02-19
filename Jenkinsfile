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
            post {
            always {
              junit 'target/surefire-reports/*.xml'
              jacoco execPattern: 'target/jacoco.exec'
            }
            }
        }
      stage('Docker build and push') {
      steps {
        withDockerRegistry([credentialsId: "docker-hub", url: ""]){
        sh 'printenv'
        sh 'docker build -t karthikdevopscloud1984/numberic-app:""$GIT_COMMIT"" .'
        sh 'docker push karthikdevopscloud1984/numberic-app:""$GIT_COMMIT""'
      }
      }
  }   
    }
}
