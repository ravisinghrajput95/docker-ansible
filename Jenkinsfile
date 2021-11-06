pipeline{
    agent any
    environment {
       DOCKER_TAG = getVersion()
    }

    stages{
        stage('Code checkout'){
        steps
        {
            git credentialsId: 'GitHub',
            url: 'https://github.com/rajputravisingh/dockeransiblejenkins.git'
        }
        }
        
        stage('Maven-Build'){
            steps{
                sh 'mvn clean package'
            }
        }
        
        stage('Docker-build'){
            steps{
                sh 'docker build -t rajputmarch2020/sample-app:${DOCKER_TAG} .'
            }
        }
        
        stage('DockerHub-image-push'){
            steps{
                withCredentials([string(credentialsId: 'dockerhub', variable: 'password')]) {
                sh 'docker login -u rajputmarch2020 -p ${password} '
                }
                sh 'docker push rajputmarch2020/sample-app:${DOCKER_TAG}'
            }
        }
        
        stage('Ansible-Dev-Deployment'){
            steps{
                ansiblePlaybook credentialsId: 'dev-server', 
                disableHostKeyChecking: true, 
                extras: "-e DOCKER_TAG=${DOCKER_TAG}", 
                installation: 'ansible', 
                inventory: 'dev.inv',
                playbook: 'deploy-docker.yml'
                
                
            }
        }
    }
}
def getVersion(){
   def commitHash =  sh returnStdout: true, script: 'git rev-parse --short HEAD'
   return commitHash
}

