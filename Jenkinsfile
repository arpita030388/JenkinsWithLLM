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
            // ✅ Securely archive logs without rawBuild
            archiveArtifacts artifacts: 'target/*.txt', fingerprint: true

            // ✅ Publish HTML reports (TestNG and Extent)
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

            // ✅ Publish Allure results if available
            allure([
                includeProperties: false,
                jdk: '',
                results: [[path: 'target/allure-results']]
            ])
        }
    }
}
