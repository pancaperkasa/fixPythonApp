pipeline {
    agent any
    stages {
       
        stage('Misconfiguration Scanner') {
            steps {
                sh """
                mkdir /var/www/html/trivy/pipeline${BUILD_NUMBER}/
                touch /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportconfig.html
                trivy config . --format template --template "@html.tpl" -o /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportconfig.html --exit-code 0 --ignore-unfixed --severity HIGH,CRITICAL
                """
            }
        }
        stage('Build Image') {
            steps {
                sh """
                docker image rm gossip-app/devsecops:v${BUILD_NUMBER} || echo "No existing image found"
                docker build --no-cache -t gossip-app/devsecops:v${BUILD_NUMBER} -f app/deploy/Dockerfile .
                """
            }
        }
        stage('Vulnerability Image Scanner') {
            steps {
                sh """
                touch /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportimagesecretpython.html
                trivy image --format template --template "@html.tpl" -o /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportimagesecretpython.html --exit-code 0 --ignore-unfixed --severity HIGH,CRITICAL gossip-app/devsecops:v${BUILD_NUMBER}
                """
            }
        }
    }
}