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
                sh "docker build . -t vamshikrishna42/helloapp:${DOCKER_TAG} "
                withCredentials([usernamePassword(credentialsId: 'dockercrd', passwordVariable: 'Docker_pwd', usernameVariable: 'Docker_user')]) {
                    sh "docker login -u vamshikrishna42  -p ${Docker_pwd}"
                }
                sh "docker push vamshikrishna42/helloapp:${DOCKER_TAG} "
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
