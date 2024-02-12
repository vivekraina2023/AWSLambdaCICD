pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                script {
                    docker.image('node:latest').inside {
                        sh 'npm install'
                    }
                }
            }
        }
    }
}
