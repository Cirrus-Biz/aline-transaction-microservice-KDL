pipeline {
    environment {
        DOCKERHUB_CREDENTIALS=credentials("Dockerhub")
    }
    agent any

    tools {
        maven "MAVEN"
    }

    stages {
        stage("Build MVN") {
            steps {
                bat "mvn -Dmaven.test.failure.ignore=true clean package"
            }
        }

        stage('SonarQube Analysis') {
            environment{
                 def mvn = tool 'MAVEN';
            }
            steps{
                withSonarQubeEnv(installationName: "sonarqube") {
                bat "cd .."
                bat "mvn clean verify sonar:sonar -Dsonar.projectKey=Sonarqube-transaction"
                }
            }       
        }


        stage("Build Docker"){
            steps{
                bat "docker build -t laxwalrus/capstone-transaction:$BUILD_NUMBER ."

            }            
        }


        stage("login to docker"){
            steps{
                bat "echo $DOCKERHUB_CREDENTIALS_PSW| docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin"
            }
        }

        stage("Deploy Docker"){
            steps{
                bat "docker push laxwalrus/capstone-transaction:$BUILD_NUMBER"
            }
        }
            


        stage("Cleaning"){
            steps{
                bat "docker logout"
            }
        }
  
        stage('Archive') {
            steps {
            archiveArtifacts artifacts: 'transaction-microservice/target/*.jar', followSymlinks: false
            }
        }
    }
}