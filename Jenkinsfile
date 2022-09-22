pipeline{
    agent any
    environment {
      DOCKER_TAG = getVersion()
    }
    stages{
        stage('init'){
            steps{
                checkout scm
            }
        }
        
        stage('Build'){
            steps{
                sh "mvn clean package"
                sh "sudo docker build . -t vamshikrishna42/helloapp:${DOCKER_TAG} "
                sh "sudo docker push vamshikrishna42/helloapp:${DOCKER_TAG} "
            }
        }
        
        stage('Deploy'){
            steps{
              ansiblePlaybook credentialsId: 'ansibleserver', disableHostKeyChecking: true, extras: "-e DOCKER_TAG=${DOCKER_TAG}", installation: 'ansible', inventory: 'dev.inv', playbook: 'deploy-docker.yml'
            }
        }
    }
}

def getVersion(){
    def commitHash = sh label: '', returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHash
}
