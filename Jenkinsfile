pipeline {
    agent any
    environment{
        registry="436211673009.dkr.ecr.us-east-1.amazonaws.com/testing"
    }
    stages {
       // stage('Checkout from git') {
        //    steps {
         //       checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[credentialsId: 'personalcreds', url: 'https://github.com/rakeshaluvala/myPythonDockerRepo.git']]])
           // }
       // }
        stage('Build Docker Image'){
            steps{
                script {
                    dockerImage = docker.build('registry', "-f Dockerfile .")
                }
            }
        }
        stage('push image to ecr repo'){
            steps{
                script {
                    sh '''
                    aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 436211673009.dkr.ecr.us-east-1.amazonaws.com
                    docker push 436211673009.dkr.ecr.us-east-1.amazonaws.com/testing:latest
                    '''
                }
            }
        }
        stage('Deploy it to kops cluster') {
            steps{
                script{
                    sh '''
                    kops export kubecfg --admin --state=s3://kubekops.in
                    kubectl apply -f  eks-deploy-from-ecr.yaml
                    sleep 10
                    kubectl get deployments
                    kubectl get pods
                    kubectl describe pod my-python-app-deployment-f9c55d9d8-w49mg
                    '''
                }                
            }
        }
    }
    
}
