pipeline {
    agent any

    environment {
        JMETER_BIN = "C:\\Users\\shari\\Documents\\apache-jmeter-5.6.3\\apache-jmeter-5.6.3\\bin\\jmeter.bat"
        WORKSPACE_DIR = "${WORKSPACE}"
        RESULTS_DIR = "${WORKSPACE}\\results"
        JMX_FILE = "${WORKSPACE}\\jmeter\\SCR01_Petstore.jmx"
        JTL_FILE = "${WORKSPACE}\\results\\result.jtl"
        REPORT_DIR = "${WORKSPACE}\\results\\html-report"
        EMAIL_TO = "sahanaexpleo@gmail.com"
    }

    stages {

        stage('Start Notification') {
            steps {
                emailext (
                    subject: "JMeter Test STARTED - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                    body: """Performance test has started.

Job: ${env.JOB_NAME}
Build: ${env.BUILD_NUMBER}
Workspace: ${env.WORKSPACE}
""",
                    to: "${EMAIL_TO}"
                )
            }
        }

        stage('Prepare Results Folder') {
            steps {
                bat """
                if exist "%RESULTS_DIR%" rmdir /s /q "%RESULTS_DIR%"
                mkdir "%RESULTS_DIR%"
                """
            }
        }

        stage('Verify Files') {
            steps {
                bat """
                echo Checking JMeter path...
                if not exist "%JMETER_BIN%" (
                    echo ERROR: JMeter not found
                    exit 1
                )

                echo Checking JMX file...
                if not exist "%JMX_FILE%" (
                    echo ERROR: JMX file not found
                    dir "%WORKSPACE%\\jmeter"
                    exit 1
                )
                """
            }
        }

        stage('Run JMeter Test') {
            steps {
                bat """
                "%JMETER_BIN%" -n ^
                -t "%JMX_FILE%" ^
                -l "%JTL_FILE%" ^
                -e -o "%REPORT_DIR%"
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
                body: """Test completed successfully.

View Report in Jenkins.

Job: ${env.JOB_NAME}
Build: ${env.BUILD_NUMBER}
""",
                to: "${EMAIL_TO}"
            )
        }

        failure {
            emailext (
                subject: "FAILURE: JMeter Test Failed - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """Test FAILED.

Check:
- Console Output
- JMeter Logs
- HTML Report

Job: ${env.JOB_NAME}
Build: ${env.BUILD_NUMBER}
""",
                to: "${EMAIL_TO}"
            )
        }

        always {
            emailext (
                subject: "JMeter Test COMPLETED - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """Execution finished.

Check Jenkins for reports.

Job: ${env.JOB_NAME}
Build: ${env.BUILD_NUMBER}
""",
                to: "${EMAIL_TO}"
            )
        }
    }
}
