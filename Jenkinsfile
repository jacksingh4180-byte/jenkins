pipeline {
    agent any

    tools {
        maven 'maven'
        nodejs 'nodejs'
    }

    environment {
        SNYK_TOKEN = credentials('snyk-token')
    }

    stages {
        stage('Build') {
            steps {
                echo "🛠️ Building Maven project..."
                sh 'mvn -v'
                sh 'mvn clean package'
            }
        }

        stage('Snyk Security Scan') {
            steps {
                script {
                    echo "🔍 Running Snyk security scan..."

                    sh '''
                    if ! command -v snyk >/dev/null 2>&1; then
                        echo "Installing Snyk CLI globally..."
                        npm install -g snyk
                    fi
                    '''

                    sh 'snyk auth $SNYK_TOKEN'

                    sh 'snyk test --file=pom.xml --severity-threshold=medium'

                    sh 'snyk test --json > snyk-report.json || true'
                }
            }
        }

        stage('Archive Report') {
            steps {
                archiveArtifacts artifacts: 'snyk-report.json', onlyIfSuccessful: false
                echo "📦 Snyk scan report archived."
            }
        }
    }

    post {
        success {
            echo "✅ Build and Snyk scan completed successfully!"
        }
        failure {
            echo "❌ Pipeline failed — check logs or Snyk report."
        }
    }
}
