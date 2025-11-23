pipeline {
    agent any

    tools {
        maven 'maven-3.9.9'   // <-- MUST match the name you used in Jenkins UI
    }

    environment {
        SNYK_TOKEN = credentials('snyk-token')
    }

    stages {

        stage('Build') {
            steps {
                sh 'mvn -version'
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Install Snyk CLI') {
            steps {
                sh '''
                    curl -sL https://static.snyk.io/cli/latest/snyk-linux -o snyk
                    chmod +x snyk
                    mv snyk /usr/local/bin/snyk
                '''
            }
        }

        stage('Authenticate Snyk') {
            steps {
                sh 'snyk auth $SNYK_TOKEN'
            }
        }

        stage('Snyk Scan') {
            steps {
                sh '''
                    snyk test --file=pom.xml \
                        --severity-threshold=medium \
                        --json > snyk-report.json || true
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
