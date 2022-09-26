
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
                trivy config . --format template --template "@deploy/html.tpl" -o /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportconfig.html 
                '''
            }
        }

        stage("Filesystem Scanner"){
            steps{
                sh '''
                cd app/
                touch /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportfilesystem.html
                trivy config . --format template --template "@deploy/html.tpl" -o /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportfilesystem.html 
                '''
            }
        }

        stage("Vulnerability and Secret Scanner"){
            steps{
                sh '''
                cd deploy/
                trivy image --format template --template "@html.tpl" -o /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportimagesecret.html gosip-app:local
                '''
            }
        }

        stage("Docker Compose and Rootfs Scanner"){
            steps{
                sh '''
                cd deploy/
                touch /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportrootfs.html
                docker-compose up -d
                '''
            }
        }
        
      
    }
}