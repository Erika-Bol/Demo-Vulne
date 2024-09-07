pipeline {
    agent any

    parameters {
        string(name: 'BRANCH_NAME', defaultValue: 'main', description: 'Branch to build')
        string(name: 'ENVIRONMENT', defaultValue: 'staging', description: 'Deployment environment')
        booleanParam(name: 'RUN_TESTS', defaultValue: true, description: 'Run tests')
    }

    environment {
        MY_ENV_VAR = 'value'
        DEP_TRACK_URL = 'http://dependency-track:8080' // URL ajustada para la red Docker
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: "${params.BRANCH_NAME}", url: 'https://github.com/CarolGuay/DemoTest.git'
            }
        }

        stage('Build') {
            steps {
                echo "Building branch ${params.BRANCH_NAME}..."
                sh 'echo "Build step"'
            }
        }

        stage('Test') {
            when {
                expression { params.RUN_TESTS }
            }
            steps {
                echo 'Testing...'
                sh 'echo "Test step"'
            }
        }

        stage('Dependency-Track Analysis') {
            steps {
                dependencyTrackPublisher(
                    serverUrl: "${env.DEP_TRACK_URL}",
                    projectName: 'DemoTest',
                    version: '1.0',
                    scanPath: '.',
                    format: 'JSON'
                )
            }
        }

        stage('Locate Analysis File') {
            steps {
                sh 'find . -name "*.json"'
            }
        }

        stage('Archive Results') {
            steps {
                echo 'Archiving Dependency Track results...'
                archiveArtifacts artifacts: '**/dependency-track-analysis.json', allowEmptyArchive: true
            }
        }    

        stage('Deploy') {
            steps {
                echo "Deploying to environment ${params.ENVIRONMENT}..."
                sh 'echo "Deploy step"'
            }
        }
    }

    post {
        always {
            echo 'Cleaning up...'
        }

        success {
            echo 'Pipeline succeeded!'
        }

        failure {
            echo 'Pipeline failed!'
        }

        unstable {
            echo 'Pipeline is unstable.'
        }
    }
}
