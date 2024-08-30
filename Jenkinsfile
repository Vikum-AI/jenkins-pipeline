pipeline {
    agent any
    
    environment {
        DIRECTORY_PATH = '/path/to/code/directory'
        TESTING_ENVIRONMENT = 'ProdTest'
        PRODUCTION_ENVIRONMENT = 'Vikum_Prod'
    }
    
    stages {
        stage('Build') {
            steps {
                echo "Fetch the source code from the directory path specified by the environment variable: ${env.DIRECTORY_PATH}"
                echo "Compile the code and generate any necessary artifacts"
                mail bcc: '', body: 'Test', subject: 'Test', to: 'vikumdabare@gmail.com'
            }
        }
        
        stage('Test') {
            steps {
                script {
                    try {
                        echo "Running unit tests"
                        echo "Running integration tests"
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        throw e
                    } finally {
                        sendEmail('Test')
                    }
                }
            }
        }
        
        stage('Code Quality Check') {
            steps {
                script {
                    try {
                        echo "Check the quality of the code"
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        throw e
                    } finally {
                        sendEmail('Code Quality Check')
                    }
                }
            }
        }
        
        stage('Deploy') {
            steps {
                echo "Deploy the application to a testing environment specified by the environment variable: ${env.TESTING_ENVIRONMENT}"
            }
        }
        
        stage('Approval') {
            steps {
                echo "Waiting for manual approval..."
                sleep 10
            }
        }
        
        stage('Deploy to Production') {
            steps {
                script {
                    try {
                        echo "Deploy the code to the production environment: ${env.PRODUCTION_ENVIRONMENT}"
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        throw e
                    } finally {
                        sendEmail('Deploy to Production')
                    }
                }
            }
        }
    }
}

def sendEmail(stageName) {
    def buildStatus = currentBuild.result ?: 'SUCCESS'
    def log = currentBuild.rawBuild.getLog(100).join("\n")
    def recipient = env.GIT_AUTHOR_EMAIL ?: env.GIT_COMMITTER_EMAIL ?: 'vikumdabare@gmail.com' // Fallback email

    emailext (
        to: "${recipient}",
        subject: "Jenkins Pipeline: ${stageName} Stage - ${buildStatus}",
        body: """<p>${stageName} Stage completed with status: ${buildStatus}</p>
                <p>Logs are attached.</p>""",
        attachLog: true,
        mimeType: 'text/html',
        compressLog: true
    )
}
