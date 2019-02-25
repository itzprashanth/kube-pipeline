#!groovy​
podTemplate(label: 'pod-hugo-app', containers: [
    containerTemplate(name: 'docker', image: 'itzprashanth/mydocker', ttyEnabled: true, command: 'cat',
        envVars: [containerEnvVar(key: 'DOCKER_CONFIG', value: '/tmp/'),])],
        volumes: [hostPathVolume(hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock')
  ]) {

    node('pod-hugo-app') {

        stage('Clone Repo') {
            checkout scm
            container('docker') {
                stage('Maven Package') {
                    sh ("mvn clean package -DskipTests")
                }
                stage('Create Docker Image') {
                    sh 'cat alert-shape-215614-dd9275aa4a50.json | docker login -u _json_key --password-stdin https://gcr.io'
                    sh 'docker build -t gcr.io/alert-shape-215614/hello-docker:${BUILD_NUMBER} .'
                    sh 'docker push gcr.io/alert-shape-215614/hello-docker:${BUILD_NUMBER}'
                }
                stage ('Connect Kubernetes Cluster ') {
		            sh 'gcloud auth activate-service-account --key-file alert-shape-215614-d9123cb13c3e.json'
		            sh 'gcloud container clusters get-credentials standard-cluster-1 --zone us-central1-a --project alert-shape-215614'
                }
            }
        }        
    }
}