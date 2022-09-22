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
                sh "sudo docker build . -t devopsforyou94/helloapp:${DOCKER_TAG} "
               withCredentials([usernamePassword(credentialsId: 'dockercrd', passwordVariable: 'Docker_pwd', usernameVariable: 'Docker_user')]) {
                    sh "sudo docker login -u devopsforyou94  -p ${Docker_pwd}"
                }
                
                sh "sudo docker push devopsforyou94/helloapp:${DOCKER_TAG} "
            }
        }
        
        stage('Deploy'){
            steps{
              ansiblePlaybook credentialsId: '496c76de-ea13-4d0f-a945-fec687b54851', disableHostKeyChecking: true, extras: "-e DOCKER_TAG=${DOCKER_TAG}", installation: 'ansible', inventory: 'dev.inv', playbook: 'deploy-docker.yml'
            }
        }
    }
}

def getVersion(){
    def commitHash = sh label: '', returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHash
}
