#!groovy​
podTemplate(label: 'techmart', containers: [
    containerTemplate(name: 'docker', image: 'itzprashanth/mydocker', ttyEnabled: true, command: 'cat',
        envVars: [containerEnvVar(key: 'DOCKER_CONFIG', value: '/tmp/'),])],
        volumes: [hostPathVolume(hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock')
  ]) {

    node('techmart') {

        stage('Clone Repo') {
            checkout scm
            container('docker') {
                stage('Maven Package') {
                    sh ("mvn clean package -DskipTests")
                }
                stage('Create Docker Image') {
                    sh 'cat alert-shape-215614-dd9275aa4a50.json | docker login -u _json_key --password-stdin https://gcr.io'
                    /* The alert.json file is created by gcloud.
                    # To create the file console >> IAM and Admin >> Service Account >>  Create new service Account
                    # Enter service account name and id >> Click on Create
                    # Assign Kubernetes Admin and Container Analysis Admin roles
                    */sh 'docker build -t gcr.io/alert-shape-215614/hello-docker:${BUILD_NUMBER} .'
                    sh 'docker push gcr.io/alert-shape-215614/hello-docker:${BUILD_NUMBER}'
                }
                stage ('Connect Kubernetes Cluster ') {
		            sh 'gcloud auth activate-service-account --key-file alert-shape-215614-dd9275aa4a50.json'
		            sh 'gcloud container clusters get-credentials elastic-gke-logging-cluster --zone us-west1-a --project alert-shape-215614'
                }
                stage ('Deploy the App ') {
                    sh 'sed -i s/hello-docker:10/hello-docker:${BUILD_NUMBER}/g test-app.yaml'
                    sh 'kubectl apply -f test-app.yaml'
                }
            }
        }        
    }
}