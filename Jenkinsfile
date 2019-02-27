def label = "worker-${UUID.randomUUID().toString()}"

podTemplate(label: label, containers: [
  containerTemplate(name: 'carbon-jessie', image: 'node:carbon-jessie', command: 'cat', ttyEnabled: true),
  containerTemplate(name: 'docker', image: 'docker', command: 'cat', ttyEnabled: true),
  containerTemplate(name: 'kubectl', image: 'lachlanevenson/k8s-kubectl:v1.8.8', command: 'cat', ttyEnabled: true),
],
serviceAccount: 'jenkins-team2',
volumes: [
  hostPathVolume(mountPath: '/home/carbon-jessie/.carbon-jessie', hostPath: '/tmp/jenkins/.carbon-jessie'),
  hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock')
]) {

node(label) {
    echo "Your Pipeline works!"
    def myRepo = checkout scm
    def gitCommit = myRepo.GIT_COMMIT
    
    stage ('testing') {
        container('carbon-jessie') {
        sh ('npm install')
        sh ('npm test')
        } 
    }

    stage('Create Docker images') {
      container('docker') {
        withCredentials([[$class: 'UsernamePasswordMultiBinding',
          credentialsId: 'dockerhub',
          usernameVariable: 'DOCKER_HUB_USER',
          passwordVariable: 'DOCKER_HUB_PASSWORD']]) {
          sh """
            docker login -u ${DOCKER_HUB_USER} -p ${DOCKER_HUB_PASSWORD}
            docker build -t antonit/dojo-cicd:${gitCommit} .
            docker push antonit/dojo-cicd:${gitCommit}
            """
        }
      }
    }

    stage('Run kubectl') {
      container('kubectl') {
        sh "kubectl get pods"
        sh "kubectl run --image=antonit/dojo-cicd:${gitCommit} dateapp --namespace jenkins-team2"
      }
    }
}
}