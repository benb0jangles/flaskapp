pipeline {
environment {  
registry = "benb0jangles/flaskapp"  
registryCredential = 'dockerhub_id'
dockerImage = ''
HOME = "${env.WORKSPACE}"
}
agent any
    stages {
    stage ('Testing')
        {
            steps{
            sh 'pip install -r requirements.txt'
            sh 'pytest-3 --junitxml results.xml'
            }
        }

    stage('Building our image') {
    steps{
    script {
    dockerImage = docker.build registry + ":$BUILD_NUMBER"
    }
    }
    }

    stage('Deploy our image') {
    steps{
    script {
    docker.withRegistry( '', registryCredential ) {
    dockerImage.push()
    }
    }
    }
    }
stage('Deploy to swarm')
    {
    steps
        {
            sshPublisher(publishers: [sshPublisherDesc(configName: 'SwarmMaster', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'cd /home/jenkins/swarm/ && docker stack deploy -c home/jenkins/swarm/docker-compose.yaml flask-app', execTimeout: 120000, flatten: true, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '/home/jenkins/swarm/', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '**/docker-compose.yaml')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: true)])
        }    
    }
stage('Cleaning up') {
steps{
sh "docker rmi $registry:$BUILD_NUMBER"
}
}
}
    post {
        always {
            junit "*.xml"
        }
    }
}
