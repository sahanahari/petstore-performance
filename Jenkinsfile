pipeline {
    agent any

    environment {
        // ✅ FIXED JMeter path (your actual path)
        JMETER_HOME = "C:\\Users\\shari\\Documents\\apache-jmeter-5.6.3\\apache-jmeter-5.6.3"

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

        stage('Prepare Results Folder') {
            steps {
                bat """
                if exist "%WORKSPACE%\\%RESULTS_DIR%" rmdir /s /q "%WORKSPACE%\\%RESULTS_DIR%"
                mkdir "%WORKSPACE%\\%RESULTS_DIR%"
                """
            }
        }

        stage('Run JMeter Test') {
            steps {
                bat """
                "%JMETER_HOME%\\bin\\jmeter.bat" -n ^
                -t "%WORKSPACE%\\${JMX_FILE}" ^
                -l "%WORKSPACE%\\${JTL_FILE}" ^
                -e -o "%WORKSPACE%\\${REPORT_DIR}"
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
