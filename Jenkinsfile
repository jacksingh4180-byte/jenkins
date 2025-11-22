pipeline {
    agent any

    tools {
        maven 'maven'
    }

    environment {
        SNYK_TOKEN = credentials('snyk-token')
    }

    stages {
        stage('Build') {
            steps {
                sh 'mvn -v'
                sh 'mvn clean package'
            }
        }

        stage('Snyk Security Scan') {
            steps {
                script {
                    sh '''
                        # Ensure Node.js exists
                        if ! command -v npm >/dev/null 2>&1; then
                            echo "Installing Node.js..."
                            curl -fsSL https://deb.nodesource.com/setup_18.x | bash -
                            apt-get install -y nodejs
                        fi

                        echo "Installing Snyk CLI..."
                        npm install -g snyk

                        snyk auth $SNYK_TOKEN
                        snyk test --file=pom.xml --severity-threshold=medium --json > snyk-report.json || true
                    '''
                }
            }
        }

        stage('Archive Report') {
            steps {
                archiveArtifacts artifacts: 'snyk-report.json', onlyIfSuccessful: false
            }
        }
    }

    post {
        success { echo '✅ Build & Snyk scan complete' }
        failure { echo '❌ Check logs or Snyk report' }
    }
}
