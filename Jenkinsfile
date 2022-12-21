pipeline {
    agent any
    stages {
       
        stage('Misconfig Scanner') {
            steps {
                sh """
                mkdir /var/www/html/trivy/pipeline${BUILD_NUMBER}/
                touch /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportconfig.html
                touch /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportconfig.json
                trivy config . -f json -o /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportconfig.json
                trivy config . --format template --template "@html.tpl" -o /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportconfig.html --exit-code 0 --severity HIGH,CRITICAL
                """
            }
        }
        stage('Create Image') {
            steps {
                sh """
                docker image rm gossip-app/devsecops:v${BUILD_NUMBER} || echo "No existing image found"
                docker build --no-cache -t gossip-app/devsecops:v${BUILD_NUMBER} -f deploy/Dockerfile .
                """
            }
        }
        stage('Vulnerability and Secret Scanner') {
            steps {
                sh """
                touch /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportimagesecretpython.html
                touch /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportimagesecretpython.json
                trivy image -f json -o /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportimagesecretpython.json gossip-app/devsecops:v${BUILD_NUMBER}
                trivy image --format template --template "@html.tpl" -o /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportimagesecretpython.html --exit-code 0 --severity HIGH,CRITICAL gossip-app/devsecops:v${BUILD_NUMBER}
                """
            }
        }
    }
}