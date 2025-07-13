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

        stage('Prepare Target Folder') {
            steps {
                echo 'Ensuring target folder exists...'
                bat 'mkdir target'
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
                echo 'Running tests...'
                bat 'mvn test > target/testLog.txt 2>&1'
            }
        }
    }

    post {
        always {
            // Archive all log files
            archiveArtifacts artifacts: 'target/*.txt', fingerprint: true

            // Publish TestNG report
            publishHTML(target: [
                reportDir: 'target/surefire-reports',
                reportFiles: 'index.html',
                reportName: 'TestNG HTML Report',
                keepAll: true,
                alwaysLinkToLastBuild: true
            ])

            // Publish Extent report
            publishHTML(target: [
                reportDir: 'target/test-output',
                reportFiles: 'ExtentReport.html',
                reportName: 'Extent HTML Report',
                keepAll: true,
                alwaysLinkToLastBuild: true
            ])

            // Publish Allure results
            allure([
                includeProperties: false,
                jdk: '',
                results: [[path: 'target/allure-results']]
            ])

            // Capture full Jenkins console output
            script {
                def fullLog = currentBuild.rawBuild.getLog(9999).join('\n')
                def logFile = new File("${env.WORKSPACE}\\target\\fullConsoleLog.txt")
                logFile.text = fullLog
            }
        }
    }
}
