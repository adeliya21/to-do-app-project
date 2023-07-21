pipeline {
    agent any

    stages {
        stage('run app on docker') {
            agent {
                docker {
                    image 'node:12-alpine'
                }
            }
            steps {
                withEnv(['HOME=${env.WORKSPACE}']) {
                    sh 'yarn install --production'
                    sh 'npm install'
                }
            }
        }

    }
}