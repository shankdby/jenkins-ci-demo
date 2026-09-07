pipeline {
    agent any

    environment {
        APP_NAME   = 'HelloCI'
        DEPLOY_DIR = 'deploy'
    }

    options {
        timestamps()
        buildDiscarder(logRotator(numToKeepStr: '10'))
    }

    stages {

        stage('Checkout') {
            steps {
                echo '=== STAGE 1: CHECKOUT - fetching latest source from GitHub ==='
                checkout scm
                bat 'dir src'
            }
        }

        stage('Build') {
            steps {
                echo '=== STAGE 2: BUILD - compiling Java sources with javac ==='
                bat 'if not exist build mkdir build'
                bat 'javac -d build src/HelloCI.java'
                bat 'dir build'
            }
        }

        stage('Test') {
            steps {
                echo '=== STAGE 3: TEST - running the application and verifying output ==='
                bat 'java -cp build HelloCI > test-output.txt'
                bat 'type test-output.txt'
                bat 'findstr /C:"Hello from Jenkins" test-output.txt'
                echo 'TEST PASSED: expected output string was found.'
            }
        }

        stage('Deploy') {
            steps {
                echo '=== STAGE 4: DEPLOY - packaging JAR and deploying artifact ==='
                bat 'jar cfe %APP_NAME%.jar HelloCI -C build .'
                bat 'if not exist %DEPLOY_DIR% mkdir %DEPLOY_DIR%'
                bat 'copy /Y %APP_NAME%.jar %DEPLOY_DIR%'
                bat 'java -jar %APP_NAME%.jar'
                archiveArtifacts artifacts: '*.jar', fingerprint: true
            }
        }
    }

    post {
        success { echo "PIPELINE SUCCESS - build #${env.BUILD_NUMBER} deployed." }
        failure { echo 'PIPELINE FAILED - check the console output above.' }
        always  { echo 'Pipeline execution completed.' }
    }
}
