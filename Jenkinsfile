pipeline {
    agent any
    stages {
       
        stage('Misconfiguration Scanner') {
            steps {
                sh """
                mkdir /var/www/html/trivy/pipeline${BUILD_NUMBER}/
                touch /var/www/html/trivy/pipeline${BUILD_NUMBER}/report_misconfiguration.html
                trivy config . --format template --template "@html.tpl" -o /var/www/html/trivy/pipeline${BUILD_NUMBER}/report_misconfiguration.html--exit-code 0 --severity MEDIUM,HIGH,CRITICAL
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
                touch /var/www/html/trivy/pipeline${BUILD_NUMBER}/report_vuln_image.html
                trivy image --format template --template "@html.tpl" -o /var/www/html/trivy/pipeline${BUILD_NUMBER}/report_vuln_image.html --exit-code 0 --severity MEDIUM,HIGH,CRITICAL --ignore-unfixed gossip-app/devsecops:v${BUILD_NUMBER}
                """
            }
        }
    }
}