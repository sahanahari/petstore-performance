pipeline {

    agent any

    environment {
        JMETER_HOME = 'C:\\Users\\shari\\Documents\\apache-jmeter-5.6.3\\apache-jmeter-5.6.3'
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Start Notification') {
            steps {
                emailext(
                    to: 'sahanaexpleo@gmail.com',
                    subject: "🚀 STARTED: JMeter Test - Build ${env.BUILD_NUMBER}",
                    body: """
JMeter Test Started

Job Name  : ${env.JOB_NAME}
Build No  : ${env.BUILD_NUMBER}
Workspace : ${env.WORKSPACE}
"""
                )
            }
        }

        stage('Prepare Results Folder') {
            steps {
                bat '''
                if exist reports rmdir /s /q reports
                mkdir reports
                '''
            }
        }

        stage('Verify JMeter & JMX') {
            steps {
                bat '''
                echo Checking JMeter...

                if not exist "%JMETER_HOME%\\bin\\jmeter.bat" (
                    echo ERROR: jmeter.bat NOT FOUND
                    exit 1
                )

                echo Checking JMX file...

                if not exist "%WORKSPACE%\\jmeter\\SCR01_Petstore.jmx" (
                    echo ERROR: JMX file NOT FOUND
                    dir "%WORKSPACE%\\jmeter"
                    exit 1
                )
                '''
            }
        }

        stage('Run JMeter Test') {
            steps {
                bat '''
                set REPORT_DIR=reports\\html_%BUILD_NUMBER%
                set JTL_FILE=reports\\result_%BUILD_NUMBER%.jtl

                echo =========================================
                echo Running JMeter Test...
                echo =========================================

                call "%JMETER_HOME%\\bin\\jmeter.bat" -n ^
                 -t "%WORKSPACE%\\jmeter\\SCR01_Petstore.jmx" ^
                 -l "%JTL_FILE%"

                echo Generating HTML Report...

                call "%JMETER_HOME%\\bin\\jmeter.bat" -g "%JTL_FILE%" -o "%REPORT_DIR%"

                echo =========================================
                echo JMeter Execution Completed
                echo =========================================
                '''
            }
        }

        stage('Publish HTML Report') {
            steps {
                publishHTML([
                    reportDir: "reports/html_${env.BUILD_NUMBER}",
                    reportFiles: 'index.html',
                    reportName: "JMeter Report - Build ${env.BUILD_NUMBER}",
                    keepAll: true,
                    alwaysLinkToLastBuild: true,
                    allowMissing: true
                ])
            }
        }
    }

    post {

        always {
            archiveArtifacts artifacts: 'reports/**/*.*', fingerprint: true
        }

        success {
            emailext(
                to: 'sahanaexpleo@gmail.com',
                subject: "✅ SUCCESS: JMeter Test - Build ${env.BUILD_NUMBER}",
                body: """
JMeter Test SUCCESS

Job Name  : ${env.JOB_NAME}
Build No  : ${env.BUILD_NUMBER}
Build URL : ${env.BUILD_URL}
"""
            )
        }

        failure {
            emailext(
                to: 'sahanaexpleo@gmail.com',
                subject: "❌ FAILURE: JMeter Test - Build ${env.BUILD_NUMBER}",
                body: """
JMeter Test FAILED

Job Name  : ${env.JOB_NAME}
Build No  : ${env.BUILD_NUMBER}
Build URL : ${env.BUILD_URL}

Check:
- Console Output
- JMeter Logs
"""
            )
        }
    }
}
