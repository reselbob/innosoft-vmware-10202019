# Deployment to Docker Hub and K8S Cluster

```
node {
    stage("Get code from GitHub"){
        git branch: 'reseltraining', url: 'https://github.com/reselbob/pinger'
    }
    stage("Discovering workspace"){
        sh 'ls ${WORKSPACE}'
    }
    stage("Discovering workspace app"){
        sh 'ls ${WORKSPACE}/app'
    }
    stage("confirm that node is installed"){
        sh 'which node'
    }
    stage("install the dependencies and run the test"){
        sh '${WORKSPACE}/unit-testing.sh'
    }
    stage("Tag the Docker image"){
        sh 'docker build -t reseltraining/pinger:1.0.${BUILD_NUMBER} ${WORKSPACE}/app/'
    }
    stage("Push the Docker image"){
        withCredentials([string(credentialsId: 'DOCKER_HUB_ACCESS', variable: 'DOCKER_HUB_ACCESS')]) {
            sh 'docker login -u reseltraining -p ${DOCKER_HUB_ACCESS}'
        }
        sh 'docker push reseltraining/pinger:1.0.${BUILD_NUMBER}'
    }
    stage('Do the file substitutions'){
        sh 'cat ${WORKSPACE}/manifests/reseltraining.yaml'
        sh "sed 's/XX_VERSION_XX/1.0.${BUILD_NUMBER}/'  ${WORKSPACE}/manifests/reseltraining.yaml > ${WORKSPACE}/manifests/reseltraining_local.yaml "
        sh 'cat ${WORKSPACE}/manifests/reseltraining_local.yaml'
    }
    stage('Deploy to Kubernetes Cluster'){
        
        kubernetesDeploy(
            configs: 'manifests/reseltraining_local.yaml, manifests/service_reseltraining.yaml',
            kubeconfigId: 'KUBERNETES_CONFIG',
            enableConfigSubstitution: true
        )       
    }
}
```