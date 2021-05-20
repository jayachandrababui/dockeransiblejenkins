pipeline
 {
    agent any
    tools
    {
        maven 'maven3'
    }
    environment 
    {
    DOCKER_TAG = getVersion()
    }
    
    stages
    {
        stage('SCM')
        {
           steps
           {
                git credentialsId: 'github1', url: 'https://github.com/jayachandrababui/dockeransiblejenkins'
           }
        }
        stage('Maven Build')
        {
            steps
            {
            sh "mvn clean package"
            }
        }
        stage('Docker Build')
        {
            steps
            {
            sh "docker build . -t jayachandrababui/jayapp:${DOCKER_TAG}" 
            }
        }
        stage('DockerHub Push')
        {
            steps
            {
                withCredentials([string(credentialsId: 'dockerhub', variable: 'dockerHubPwd')])
                {
                sh "docker login -u jayachandrababui -p ${dockerHubPwd}"
                }
            sh "docker push jayachandrababui/jayapp:${DOCKER_TAG} "
        
            }   
        }
        stage('Docker Deploy')
        {
            steps
            {
            ansiblePlaybook credentialsId: 'dev-server', disableHostKeyChecking: true, extras: "-e DOCKER_TAG=${DOCKER_TAG}", installation: 'ansible', inventory: 'dev.inv', playbook: 'deploy-docker.yml'
        
            }   
        }
    }
 }


def getVersion()
{
    def commitHash = sh returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHash
}
