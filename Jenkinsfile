pipeline {
    environment {
        registry = "tiskel/asterisk-custom"
        registryCredential = 'docker_hub_creditials_id'
    }

    agent any

    stages {
        stage('Setup Multi-Arch Builder') {
            steps {
                sh 'docker run --rm --privileged multiarch/qemu-user-static --reset -p yes'
                sh 'docker buildx create --name astbuilder --driver docker-container --use 2>/dev/null || docker buildx use astbuilder'
                sh 'docker buildx inspect --bootstrap'
            }
        }

        stage('Build and Push Multi-Arch Image') {
            steps {
                script {
                    docker.withRegistry('', registryCredential) {
                        sh "docker buildx build --platform linux/amd64,linux/arm64 -t ${registry}:${BUILD_NUMBER} -t ${registry}:latest --push ."
                    }
                }
            }
        }
    }

    post {
        always {
            sh 'docker buildx rm astbuilder || true'
            cleanWs()
        }
    }
}
