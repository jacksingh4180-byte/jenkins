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
                echo 'Building Maven project...'
                sh 'mvn -v'
                sh 'mvn clean package'
            }
        }

        stage('Snyk Security Scan') {
            steps {
                script {
                    echo 'Running Snyk security scan...'

                    sh '''
                        # Install Node.js and npm if not present
                        if ! command -v npm >/dev/null 2>&1; then
                            echo "Installing Node.js..."
                            curl -fsSL https://deb.nodesource.com/setup_18.x | bash -
                            apt-get install -y nodejs
                        fi

                        # Install Snyk CLI
                        echo "Installing Snyk CLI..."
                        npm install -g snyk

                        # Authenticate Snyk CLI
                        snyk auth $SNYK_TOKEN

                        # Run vulnerability test on Maven dependencies
                        snyk test --file=pom.xml --severity-threshold=medium --json > snyk-report.json || true
                    '''
                }
            }
        }

        stage('Archive Report') {
            steps {
                echo 'Archiving Snyk report...'
                archiveArtifacts artifacts: 'snyk-report.json', onlyIfSuccessful: false
            }
        }
    }

    post {
        success {
            echo 'Build and Snyk scan completed successfully!'
        }
        failure {
            echo 'Pipeline failed — check logs or Snyk report.'
        }
    }
}
