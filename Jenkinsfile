pipeline {
    agent any

    options {
        skipDefaultCheckout(true)
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/arpita030388/JenkinsWithLLM.git'
            }
        }

        stage('Build') {
            steps {
                echo 'Compiling...'
                bat 'mvn clean compile > target/buildLog.txt 2>&1'
            }
        }

        stage('Test') {
            steps {
                echo 'Testing...'
                bat 'mvn test > target/testLog.txt 2>&1'
            }
        }
    }

    post {
        always {
                script {
                        def logFile = new File("${env.WORKSPACE}\\target\\fullConsoleLog.txt")
                        logFile.text = currentBuild.rawBuild.getLog(9999).join('\n')
                    }

            publishHTML(target: [
                reportDir: 'target/surefire-reports',
                reportFiles: 'index.html',
                reportName: 'TestNG HTML Report',
                keepAll: true,
                alwaysLinkToLastBuild: true
            ])

            publishHTML(target: [
                reportDir: 'target/test-output',
                reportFiles: 'ExtentReport.html',
                reportName: 'Extent HTML Report',
                keepAll: true,
                alwaysLinkToLastBuild: true
            ])

            allure([
                includeProperties: false,
                jdk: '',
                results: [[path: 'target/allure-results']]
            ])
        }
    }
}
