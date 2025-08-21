pipeline {
    agent any

    parameters {
        string(name: 'IMAGE_TAG', defaultValue: 'latest', description: 'Docker Image Tag to deploy')
    }

    environment {
        APP_NAME = "register-app-pipeline"
    }

    stages {
        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }

        stage("Checkout from SCM") {
            steps {
                git branch: 'main', credentialsId: 'git-cred', url: 'https://github.com/devopspractice8/gitops-register-app.git'
            }
        }

        stage("Update the Deployment Tags") {
            steps {
                sh """
                   sed -i 's|${APP_NAME}:.*|${APP_NAME}:${IMAGE_TAG}|g' deployment.yaml
                   cat deployment.yaml
                """
            }
        }

        stage("Push the changed deployment file to Git") {
            steps {
                withCredentials([usernamePassword(credentialsId: 'git-cred', usernameVariable: 'GIT_USER', passwordVariable: 'GIT_PASS')]) {
                    sh """
                       git config --global user.name "devopspractice8"
                       git config --global user.email "devopspractic05@gmail.com"
                       git add deployment.yaml
                       git commit -m "Updated Deployment Manifest with ${IMAGE_TAG}" || echo "No changes to commit"
                       git push https://${GIT_USER}:${GIT_PASS}@github.com/devopspractice8/gitops-register-app.git main
                    """
                }
            }
        }
    }
}
