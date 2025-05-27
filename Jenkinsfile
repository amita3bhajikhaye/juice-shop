pipeline {
    agent any

    environment {
        TARGET_URL = 'http://localhost:3000'  // Juice Shop default URL
    }

    stages {
        stage('Clone from GitHub') {
            steps {
                git url: 'https://github.com/amita3bhajikhaye/juice-shop.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Run App') {
            steps {
                sh 'nohup npm start &'
                sleep(time: 10, unit: 'SECONDS') // give app time to start
            }
        }

        stage('Start ZAP') {
            steps {
                sh 'docker pull ghcr.io/zaproxy/zaproxy:stable'
                sh '''
                    docker run -u zap -d --name zap \
                    -p 8090:8090 \
                    ghcr.io/zaproxy/zaproxy:stable \
                    zap.sh -daemon -host 0.0.0.0 -port 8090 -config api.disablekey=true
                '''
                sleep(time: 10, unit: 'SECONDS') // give ZAP time to initialize
            }
        }

        stage('Run ZAP Scan') {
            steps {
                sh """
                    docker exec zap zap-baseline.py \
                    -t ${env.TARGET_URL} \
                    -r zap_report.html \
                    -I
                """
            }
        }

        stage('Copy Report') {
            steps {
                sh 'docker cp zap:/zap/zap_report.html ./zap_report.html'
            }
        }

        stage('Archive Report') {
            steps {
                archiveArtifacts artifacts: 'zap_report.html', fingerprint: true
            }
        }
    }

    post {
        always {
            echo 'Cleaning up...'
            sh 'docker rm -f zap || true'
            sh 'pkill -f "node" || true' // Stop Juice Shop if still running
            cleanWs()
        }
    }
}
