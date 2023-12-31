node {
    stage('Build') {
        docker.image('node:16-buster-slim').inside('-p 3000:3000') {
            sh 'npm install'
        }
    }
    stage('Test') {
        docker.image('node:16-buster-slim').inside('-p 3000:3000') {
            sh './jenkins/scripts/test.sh'
        }
    }
    stage('Manual Approval') {
        input message: 'Lanjutkan ke tahap Deploy?'
    }
    stage('Deploy') {
        docker.image('node:16-buster-slim').inside('-p 3000:3000') {
            sh './jenkins/scripts/deliver.sh'
            sleep(time: 1, unit: 'MINUTES')
        }
    }
}
/*
pipeline {
    agent {
        docker {
            image 'node:16-buster-slim' 
            args '-p 3000:3000' 
        }
    }
    stages {
        stage('Build') { 
            steps {
                sh 'npm install'
            }
        }
        stage('Test') {
            steps {
                sh './jenkins/scripts/test.sh'
            }
        }
        stage('Deploy') {
            steps {
                sh './jenkins/scripts/deliver.sh'
                input message: 'Sudah selesai menggunakan React App? (Klik "Proceed" untuk mengakhiri)'
                sh './jenkins/scripts/kill.sh'
            }
        }
    }
}
*/
