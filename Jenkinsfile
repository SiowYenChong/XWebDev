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
                step([$class: 'Publisher',
                      reportSuccess: true,
                      alwaysPublishFromMaster: false,
                      continueOnError: false,
                      failOnError: true,
                      publishers: [
                          [$class: 'SSHPublisher',
                           configName: 'your-ssh-config', // Configure this in Jenkins
                           transfers: [
                               [$class: 'Transfer', sourceFiles: 'dist/**', removePrefix: 'dist', remoteDirectory: '.']
                           ]
                          ]
                      ]
                ])
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
