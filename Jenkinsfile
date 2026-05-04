pipeline {
    agent any

    environment {
        JMETER_HOME = "C:\\apache-jmeter-5.6.3"
        RESULTS_DIR = "results"
        JMX_FILE = "jmeter/petstore.jmx"
        JTL_FILE = "results/result.jtl"
        REPORT_DIR = "results/html-report"
        EMAIL_TO = "sahanaexpleo@gmail.com"
    }

    stages {

        stage('Start Notification') {
            steps {
                emailext (
                    subject: "JMeter Test STARTED - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                    body: "Performance test has started.\nJob: ${env.JOB_NAME}\nBuild: ${env.BUILD_NUMBER}",
                    to: "${EMAIL_TO}"
                )
            }
        }

        // ❌ REMOVED duplicate checkout stage

        stage('Prepare Results Folder') {
            steps {
                bat """
                if exist %RESULTS_DIR% rmdir /s /q %RESULTS_DIR%
                mkdir %RESULTS_DIR%
                """
            }
        }

        stage('Run JMeter Test') {
            steps {
                bat """
                %JMETER_HOME%\\bin\\jmeter -n ^
                -t %JMX_FILE% ^
                -l %JTL_FILE% ^
                -e -o %REPORT_DIR%
                """
            }
        }

        stage('Archive Results') {
            steps {
                archiveArtifacts artifacts: 'results/**', fingerprint: true
            }
        }
    }

    post {

        success {
            emailext (
                subject: "SUCCESS: JMeter Test Passed - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Test completed successfully.\nCheck report in Jenkins.\n\nJob: ${env.JOB_NAME}",
                to: "${EMAIL_TO}"
            )
        }

        failure {
            emailext (
                subject: "FAILURE: JMeter Test Failed - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Test FAILED.\nCheck console logs and report.\n\nJob: ${env.JOB_NAME}",
                to: "${EMAIL_TO}"
            )
        }

        always {
            emailext (
                subject: "JMeter Test COMPLETED - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Execution finished.\nSee results in Jenkins.",
                to: "${EMAIL_TO}"
            )
        }
    }
}
