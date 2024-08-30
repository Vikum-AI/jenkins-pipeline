pipeline {
    agent any
    
    environment {
        DIRECTORY_PATH = '/path/to/code/directory'
        TESTING_ENVIRONMENT = 'StagingEnv'
        PRODUCTION_ENVIRONMENT = 'ProductionEnv'
    }
    
    stages {
        stage('Build') {
            steps {
                echo "Fetching the source code from the directory path specified by the environment variable: ${env.DIRECTORY_PATH}"
                echo "Using Maven to compile the code and generate artifacts"
            }
        }
        
        stage('Unit and Integration Tests') {
            steps {
                script {
                    try {
                        echo "Running unit tests using JUnit"
                        echo "Running integration tests using JUnit"
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        throw e
                    } finally {
                        sendEmail('Unit and Integration Tests')
                    }
                }
            }
        }
        
        stage('Code Analysis') {
            steps {
                script {
                    try {
                        echo "Analyzing code quality using SonarQube"
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        throw e
                    } finally {
                        sendEmail('Code Analysis')
                    }
                }
            }
        }
        
        stage('Security Scan') {
            steps {
                script {
                    try {
                        echo "Performing security scan using OWASP ZAP"
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        throw e
                    } finally {
                        sendEmail('Security Scan')
                    }
                }
            }
        }
        
        stage('Deploy to Staging') {
            steps {
                echo "Deploying the application to the staging environment: ${env.TESTING_ENVIRONMENT}"
            }
        }
        
        stage('Integration Tests on Staging') {
            steps {
                echo "Running integration tests on the staging environment using JUnit"
            }
        }
        
        stage('Deploy to Production') {
            steps {
                script {
                    try {
                        echo "Deploying the application to the production environment: ${env.PRODUCTION_ENVIRONMENT}"
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
    def authorEmail = sh(
        script: "git log -1 --pretty=format:'%ae'",
        returnStdout: true
    ).trim()

    emailext (
        to: "${authorEmail}",
        subject: "Jenkins Pipeline: ${stageName} Stage - ${buildStatus}",
        body: """<p>${stageName} Stage completed with status: ${buildStatus}</p>
                <p>Logs are attached.</p>""",
        attachLog: true,
        mimeType: 'text/html',
        compressLog: true
    )
}
