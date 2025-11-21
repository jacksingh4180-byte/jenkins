pipeline {
    agent any

    tools {
        maven 'maven-3.9'
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/jenkins-docs/simple-java-maven-app.git', branch: 'master'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
            }
        }
    }
}
