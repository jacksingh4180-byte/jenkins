pipeline {
    agent {
        docker {
            image 'maven:3.9.9-eclipse-temurin-21'
            args '-u root'
        }
    }

    environment {
        SNYK_TOKEN = credentials('snyk-token')
    }

    stages {
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Snyk Security Scan') {
            steps {
                sh '''
                    apt-get update && apt-get install -y npm
                    npm install -g snyk
                    snyk auth $SNYK_TOKEN
                    snyk test --file=pom.xml --severity-threshold=medium --json > snyk-report.json || true
                '''
            }
        }

        stage('Archive Report') {
            steps {
                archiveArtifacts artifacts: 'snyk-report.json', onlyIfSuccessful: false
            }
        }
    }
}
