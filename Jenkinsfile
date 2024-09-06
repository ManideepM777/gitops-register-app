pipeline{
    agent {label "Jenkins-Agent"}
    environment{
        APP_NAME = "register-app-pipeline"
    }
    stages{
        stage("Cleanup Workspace"){
            steps{
                cleanWs()
            }
        }
        stage("Checkout from SCM"){
            steps{
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/ManideepM777/gitops-register-app'
            }
        }
        stage("Update the Deployment Tags"){
            steps{
                sh """ 
                cat deployment.yaml
                sed -i 's/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g' deployment.yaml
                cat deployment.yaml
                """
            }
        }
        stage("Push the changed deployment file to git") {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'github-https', passwordVariable: 'GITHUB_TOKEN', usernameVariable: 'GITHUB_USER')]) {
                        // Use GIT_ASKPASS to securely pass credentials
                        sh '''
                        git config --global credential.helper cache
                        git config --global credential.username $GITHUB_USER
                        git remote set-url origin https://$GITHUB_USER@github.com/ManideepM777/gitops-register-app.git
                        
                        export GIT_ASKPASS=$(mktemp)
                        echo "echo $GITHUB_TOKEN" > $GIT_ASKPASS
                        chmod +x $GIT_ASKPASS

                        git add deployment.yaml
                        git commit -m "Updated Deployment manifest"
                        git push origin HEAD:main
                        '''
                    }
                }
            }
        }
    }
}
