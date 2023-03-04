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
            // post {
            // always {
            //   junit 'target/surefire-reports/*.xml'
            //   jacoco execPattern: 'target/jacoco.exec'
            // }
            // }
        }
      stage('Mutation Tests - PIT') {
            steps {
              sh "mvn org.pitest:pitest-maven:mutationCoverage"
            }
            // post {
            // always {
            //   pitmutation mutationStatsFile: '**/target/pit-reports/**/mutations.xml'
            // }
            // }
        }
      stage('SonarQube - SAST') {
            steps {
              withSonarQubeEnv('SonarQube') {
              sh "mvn sonar:sonar \
              -Dsonar.projectKey=numberic-application \
              -Dsonar.host.url=http://devsecops-karthik.eastus.cloudapp.azure.com:9000"
            }
            timeout(time: 2, unit: 'MINUTES'){
              waitForQualityGate abortPipeline: true
            }
            }
        }

      stage('Vulnerability Scan - Docker ') {
      steps {
        parallel(
        "Dependency Scan" : { echo "Hello I am a dummy scan" },
        "Trivy Scan" : { sh "bash trivy-docker-image-scan.sh" }
        )
       }
      post {
        always {
          dependencyCheckPublisher pattern: 'target/dependency-check-report.xml'
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
    stage('Kubernetes Deployment - Dev') {
      steps {
        withKubeConfig([credentialsId: "kubeconfig"]){
        sh "sed -i 's#replace#karthikdevopscloud1984/numberic-app:${GIT_COMMIT}#g' k8s_deployment_service.yaml"
        sh "kubectl apply -f k8s_deployment_service.yaml"
      }
      }
     }    
    }
    post { 
        always { 
              junit 'target/surefire-reports/*.xml'
              jacoco execPattern: 'target/jacoco.exec'
              pitmutation mutationStatsFile: '**/target/pit-reports/**/mutations.xml'
        }
    }

    // sucess {

    // }

    // failure {

    // }
}
