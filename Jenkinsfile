#!groovy​
podTemplate(label: 'pod-hugo-app', containers: [
    containerTemplate(name: 'docker', image: 'itzprashanth/mydocker', ttyEnabled: true, command: 'cat',
        envVars: [containerEnvVar(key: 'DOCKER_CONFIG', value: '/tmp/'),])],
        volumes: [secretVolume(secretName: 'docker-config', mountPath: '/tmp'),
                  hostPathVolume(hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock')
  ]) {

    node('pod-hugo-app') {

        stage('Clone Repo') {
            checkout scm
            container('docker') {
                stage('Maven Package') {
                    sh ("mvn clean package -DskipTests")
                }
            }
        }        
    }
}