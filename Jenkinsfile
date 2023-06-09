pipeline {
    agent any
    tools {nodejs "nodejs"}
    stages {

    // Tests
    stage('Unit Tests') {
      steps{
        sh 'npm install'
	sh 'npm test -- --watchAll=false'
      }
    }
        
    // Building Docker images
    stage('Building image') {
      steps{
        sh 'docker build -t demo .'
      }
    }
   
    // Uploading Docker images into AWS ECR
    stage('Push Docker Image to ECR') {
      steps{
        withAWS(credentials: 'demo-admin-user', region: 'us-east-1') {
          sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 782983671572.dkr.ecr.us-east-1.amazonaws.com'
          sh 'docker tag demo:latest 782983671572.dkr.ecr.us-east-1.amazonaws.com/demo:latest'
          sh 'docker push 782983671572.dkr.ecr.us-east-1.amazonaws.com/demo:latest'
        }
      }
    }
      
    stage('Deploy to EKS cluster') {
      steps{
        withAWS(credentials: 'demo-admin-user', region: 'us-east-1') {
	  sh 'curl -LO "https://storage.googleapis.com/kubernetes-release/release/v1.20.5/bin/linux/amd64/kubectl"'
	  sh 'chmod u+x ./kubectl'
          sh "./kubectl apply -f eks-deploy-k8s.yaml"
        }
      }
    }
    }
}
