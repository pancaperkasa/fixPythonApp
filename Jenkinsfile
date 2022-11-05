pipeline{
    agent any
    stages{

        stage("SCM Checkout"){
            steps{
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'CleanBeforeCheckout', deleteUntrackedNestedRepositories: true]], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'trivy-pipeline', url: 'https://github.com/pancaperkasa/trivy-Jenkins.git']]])
            }
        }

        stage("Misconfig Scanner"){
            steps{
                sh '''
                cd app/
                mkdir /var/www/html/trivy/pipeline${BUILD_NUMBER}/
                touch /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportconfig.html
                trivy config . --format template --template "@deploy/html.tpl" -o /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportconfig.html --exit-code 0 --severity HIGH,CRITICAL
                '''
            }
        }

        stage('Filesystem Scanner') {
            steps {
                sh """
                cd app/
                touch /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportfs.html
                trivy fs . --format template --template "@deploy/html.tpl" -o /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportfs.html --exit-code 0 --severity HIGH,CRITICAL
                """
            }
        }

        stage('Create Image') {
            steps {
                sh """
                docker build --no-cache -t gossipapp:v${BUILD_NUMBER} -f app/deploy/Dockerfile app/
                """
            }
        }
        
        stage("Vulnerability and Secret Scanner"){
            steps{
                sh '''
                cd app/deploy
                touch /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportimagesecretpython.html
                trivy image --format template --template "@html.tpl" -o /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportimagesecretpython.html --exit-code 0 --severity HIGH,CRITICAL gossipapp:v${BUILD_NUMBER}
                '''
            }
        }
      
    }
}
