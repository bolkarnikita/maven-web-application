pipeline
{
    agent any

    tools
    {
        maven 'Maven_3.9.9'
    }

    environment
    {
        buildNumber = "${BUILD_NUMBER}"
    }

    stages
    {
        stage('Git Checkout')
        {
            steps()
            {
                git branch: 'DevOpsNovemberBatch', url: 'https://github.com/MithunTechnologiesDevOps/maven-web-application.git'
            }
        }

        stage('Build Project')
        {
            steps()
            {
                sh 'mvn clean package'
            }
        }

        stage('Build Docker Image')
        {
            steps()
            {
                sh 'docker build -t mithuntechnologies/login-service:${buildNumber} .'
            }
        }

        stage('Push Docker Image to DockerHub Repository')
        {
            steps()
            {
                withCredentials([string(credentialsId: 'Docker_Hub_Password', variable: 'Docker_Hub_Password')])
                {
                    sh 'docker login -u mithuntechnologies -p ${Docker_Hub_Password}'
                }
                sh 'docker push mithuntechnologies/login-service:${buildNumber}'
            }
        }

        stage('Remove Docker Image Locally in Jenkins')
        {
            steps()
            {
                sh 'docker rmi mithuntechnologies/login-service:${buildNumber}'
            }
        }

        stage('Deploy Application to Deployment Server')
        {
            steps()
            {
                sshagent(['DeploymentServer_SSH'])
                {
                    sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.12.147 docker rm -f maven-container || true"
                     sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.12.147 docker run -d --name maven-container -p 8080:8080 mithuntechnologies/login-service:${buildNumber}"
                }
            }
        }
    }
}