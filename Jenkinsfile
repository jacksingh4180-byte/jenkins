pipeline {
    agent any

    tools {
        maven 'maven'
    }

    stages {
        stage('Build') {
            steps {
                sh 'mvn -v'
                sh 'ls'
                sh 'mvn clean package'
            }
        }
    }
}
