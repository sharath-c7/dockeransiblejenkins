pipeline{
    agent any
    tools {
      maven 'mavenHome'
    }
    environment {
      DOCKER_TAG = getVersion()
    }
    stages{
        stage('SCM'){
            steps{
                git 'https://github.com/sharath-c7/dockeransiblejenkins'
            }
        }
        
        stage('Maven Build'){
            steps{
                sh "mvn clean package"
            }
        }
        
        stage('Docker Image Build'){
            steps{
                sh "docker build -t dockerc721/webaptest:${DOCKER_TAG} ."
            }
        }
        
        stage('Docker Image Push'){
            steps{
                withCredentials([string(credentialsId: 'docker-hub', variable: 'dockerHubPwd')]) {
                sh "docker login -u dockerc721 -p ${dockerHubPwd}" 
                }
                sh "docker push dockerc721/webaptest:${DOCKER_TAG}"
            }
        }
        
        stage('Docker Deploy'){
            steps{
                ansiblePlaybook credentialsId: 'dev-server', 
                disableHostKeyChecking: true, 
                extras: "-e DOCKER_TAG=${DOCKER_TAG}", 
                installation: 'ansibleExe', 
                inventory: 'dev.inv', 
                playbook: 'deploy-docker.yml'
            }
        }
    }
}

def getVersion(){
    def commitHash = sh returnStdout: true, script: 'git rev-parse --short HEAD'
}
