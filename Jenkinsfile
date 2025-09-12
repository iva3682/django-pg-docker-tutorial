
pipeline {
    agent any 

    stages {
        stage('Checkout') {
            steps {
                script {
                    gitTag=sh(returnStdout: true, script: "git tag --contains | head -1").trim()
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-credentials-id') {
                        def customImage = docker.build("iva3682/testapp:${gitTag}") 
                        customImage.push()
                    }
                }
            }
        }
        stage('Deploy with Helm') {
            steps {
                script {
                    withKubeConfig(credentialsId: 'k8s-credentials-id') {
                        sh "helm upgrade --install test-app ./chart --namespace test-app --create-namespace --set app.image.tag=${gitTag}"
                    }
                }
            }
        }
    }
}
