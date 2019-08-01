pipeline {
    agent any 
    stages {
        stage('Build') {
            steps {
                // Download the code
                checkout scm
                // Compile the main class
                //sh 'javac -d target -sourcepath src/main/java src/main/java/com/example/math/Calculator.java'
            }
        }
        stage('Create & tag image') {
            steps {
                // Compile the tests
		sh 'docker build --no-cache --rm --tag luisconcepcion18/nginxhellowhale:${BUILD_NUMBER} .'                
            }
        }

	stage('Dockerhub login') {
            steps {
                sh 'docker login -u luisconcepcion18 -p ${DOCKER_HUB}'                
            }
        }

	stage('Dockerhub push image') {
            steps {
                sh 'docker push luisconcepcion18/nginxhellowhale:${BUILD_NUMBER}'                
            }
        }

	stage('Deployment to k8') {
            steps {
                sh 'kubectl set image deployments/hellowhale nginxhellowhale=luisconcepcion18/nginxhellowhale:${BUILD_NUMBER}'                
            }
        }


    }
}
