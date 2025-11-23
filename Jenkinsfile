pipeline {
    agent any

    tools {
        maven 'maven'   // Name from Global Tool Config
    }

    environment {
        SNYK_TOKEN = credentials('snyk-token')   // Secret text credential
    }

    stages {

        stage('Build') {
            steps {
                sh 'mvn -version'
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
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

        stage('Snyk SCA Scan') {
            steps {
                sh '''
                    snyk test --file=pom.xml \
                        --severity-threshold=medium \
                        --json > snyk-sca-report.json \
                        || true
                '''
            }
        }

        stage('Upload Snyk Results to Dashboard') {
            steps {
                sh '''
                    snyk monitor --file=pom.xml || true
                '''
            }
        }

        stage('Archive Reports') {
            steps {
                archiveArtifacts artifacts: 'snyk-sca-report.json', onlyIfSuccessful: false
            }
        }
    }
}
