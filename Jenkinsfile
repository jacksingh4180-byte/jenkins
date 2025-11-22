pipeline {
    agent any

    tools {
        maven 'maven'
        nodejs 'nodejs'
    }pipeline {
    agent any

    tools {
        maven 'maven'
    }

    environment {
        // Securely retrieve your Snyk API token from Jenkins credentials
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
                    # Ensure Node.js and npm are installed (if not already available)
                    if ! command -v npm >/dev/null 2>&1; then
                        echo "Installing Node.js..."
                        curl -fsSL https://deb.nodesource.com/setup_18.x | bash -
                        apt-get install -y nodejs
                    fi

                    # Install Snyk CLI
                    echo "Installing Snyk CLI..."
                    npm install -g snyk

                    # Authenticate Snyk CLI using the Jenkins secret
                    snyk auth $SNYK_TOKEN

                    # Run vulnerability test on Maven dependencies
                    snyk test --file=pom.xml --severity-threshold=medium --json > snyk-report.json || true
                    '''
                }
            }
        }

        stage('Archive Report') {
            steps {
                echo "📦 Archiving Snyk report..."
                archiveArtifacts artifacts: 'snyk-report.json', onlyIfSuccessful: false
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


    environment {
        // Securely retrieve Snyk token
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

                    // Install Snyk CLI if not present
                    sh '''
                    if ! command -v snyk >/dev/null 2>&1; then
                        echo "Installing Snyk CLI globally..."
                        npm install -g snyk
                    fi
                    '''

                    // Authenticate Snyk CLI
                    sh 'snyk auth $SNYK_TOKEN'

                    // Run vulnerability test on Maven dependencies
                    sh 'snyk test --file=pom.xml --severity-threshold=medium'

                    // Generate a JSON report for Jenkins artifacts
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
