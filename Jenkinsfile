def label = "worker-${UUID.randomUUID().toString()}"

podTemplate(label: label, containers: [
  containerTemplate(name: 'carbon-jessie', image: 'node:carbon-jessie', command: 'cat', ttyEnabled: true),
],
volumes: [
  hostPathVolume(mountPath: '/home/carbon-jessie/.carbon-jessie', hostPath: '/tmp/jenkins/.carbon-jessie'),
]) {

node(label) {
    echo "Your Pipeline works!"
    checkout scm
    sh('ls -la')
    
    stage ('testing') {
        container('carbon-jessie') {
        sh ('npm install')
        sh ('npm test')
        } 
    } 
}
}