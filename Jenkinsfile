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
        stage("Push the changed deployment file to git"){
            steps{
                script {
                    withCredentials([usernamePassword(credentialsId: 'github-https', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                        sh """
                        git remote set-url origin https://$USER:$PASS@github.com/ManideepM777/gitops-register-app.git
                        git add deployment.yaml
                        git commit -m 'Updated Deployment manifest'
                        git push origin HEAD:main
                        """
                    }
                }
            }
        }
    }
}
