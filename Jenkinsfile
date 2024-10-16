pipeline {
    agent any

    parameters {
        string(name: 'IMAGE_TAG', description: 'Enter the image tag')
    }
    
    environment {
        GIT_REPO = "https://github.com/shashidas95/eticket-be-config" 
        APP_NAME = "eticket-be"
    }

    stages {
        stage('CLEANUP WORKSPACE') {
            steps {
                script {
                    cleanWs()
                }
            }
        }

        stage("CHECKOUT GIT REPO") {
            steps {
                git branch: 'master', url: "${GIT_REPO}"
            }
        }

        stage("UPDATE K8S DEPLOYMENT FILES AND PUSH TO THE GIT REPO") {
            steps {
                sh 'cat ./k8s/deployment.yaml'
                // Update the deployment file with the new IMAGE_TAG
                sh "sed -i.bak 's#image: ${APP_NAME}:.*#image: ${APP_NAME}:${IMAGE_TAG}#' ./k8s/deployment.yaml"
                sh 'cat ./k8s/deployment.yaml'

                // Configure Git
                sh 'git config --global user.email shashidas95@gmail.com'
                sh 'git config --global user.name shashidas95'
                sh 'git add ./k8s/deployment.yaml'
                sh "git commit -m 'Updated deployment files to ${IMAGE_TAG}'"

                // Push the changes
                withCredentials([usernamePassword(credentialsId: 'github', passwordVariable: 'pass', usernameVariable: 'uname')]) {
                    sh 'git push https://$uname:$pass@github.com/shashidas95/eticket-be-config.git master'
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully'
        }
        failure {
            echo 'Pipeline failed'
        }
    }
}
