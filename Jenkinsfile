pipeline {
    agent any

    stages {
        stage('Clone Juice Shop') {
            steps {
                git url: 'https://github.com/amita3bhajikhaye/juice-shop.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build Juice Shop') {
            steps {
                sh 'npm run build'
            }
        }

        stage('Start Juice Shop') {
            steps {
                sh 'npm start &'
            }
        }
    }
}
