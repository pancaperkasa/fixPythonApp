pipeline{
    agent any
    stages{

        stage("SCM Checkout"){
            steps{
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'CleanBeforeCheckout', deleteUntrackedNestedRepositories: true]], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'trivy-pipeline', url: 'https://github.com/pancaperkasa/fixPythonApp.git']]])
            }
        }

        stage("Misconfig Scanner"){
            steps{
                sh '''
                cd app/
                mkdir /var/www/html/trivy/pipeline${BUILD_NUMBER}/
                touch /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportconfig.html
                trivy config . --format template --template "@deploy/html.tpl" -o /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportconfig.html --severity HIGH,CRITICAL
                '''
            }
        }

        stage("Filesystem Scanner"){
            steps{
                sh '''
                cd app/
                touch /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportfilesystem.html
                trivy fs . --format template --template "@deploy/html.tpl" -o /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportfilesystem.html --severity HIGH,CRITICAL
                '''
            }
        }

        stage("Docker Compose and Rootfs Scanner"){
            steps{
                sh '''
                cd app/deploy/
                docker-compose up -d
                '''
            }
        }
        
        stage("Vulnerability and Secret Scanner"){
            steps{
                sh '''
                cd app/deploy
                touch /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportimagesecretpython.html
                trivy image --format template --template "@html.tpl" -o /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportimagesecretpython.html --severity HIGH,CRITICAL python:3.6
                
                touch /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportimagesecretdb.html
                trivy image --format template --template "@html.tpl" -o /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportimagesecretdb.html --severity HIGH,CRITICAL mariadb:10.6.3
                '''
            }
        }
      
    }
}