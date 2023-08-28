pipeline {
    agent any
    
    environment {
        CC_TEST_REPORTER = './cc-test-reporter'
    }

    stages {
        stage('Before Build') {
            steps {
                script {
                    sh "curl -L https://codeclimate.com/downloads/test-reporter/test-reporter-latest-linux-amd64 > ${env.CC_TEST_REPORTER}"
                    sh "chmod +x ${env.CC_TEST_REPORTER}"
                    sh "${env.CC_TEST_REPORTER} before-build"
                }
            }
        }
        
        stage('Build') {
            steps {
                script {
                    echo "hello world"
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    sh 'npm install' // Install dependencies if needed
                    sh 'npm run build' // Build your project if needed
                }
                deploy(
                    adapters: [gitPublisher(
                        branches: [[name: 'gh-pages']],
                        credentialsId: '4fc3d5f5-e38a-45c0-9daf-adce760f2f12',
                        repositoryName: 'XWebDev',
                        targetDirectory: 'dist' // Adjust this to the appropriate build output directory
                    )]
                )
            }
        }
    }

    post {
        always {
            script {
                sh "${env.CC_TEST_REPORTER} after-build --exit-code $currentBuild.result"
                sh 'codecov'
            }
        }
    }
}
