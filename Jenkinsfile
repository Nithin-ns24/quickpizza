@Library('deploy-conf') _

buildDockerImage('https://github.com/Nithin-ns24/quickpizza.git')


// vars/buildDockerImage.groovy
def call(String repoUrl) {
    properties([
        parameters([
            string(name: 'DOCKER_IMAGE_NAME', defaultValue: 'my-app', description: 'Docker Image Name'),
            string(name: 'DOCKER_IMAGE_TAG', defaultValue: 'latest', description: 'Docker Image Tag')
        ])
    ])

    node {
        stage('Checkout Code') {
            echo "Cloning repo: ${repoUrl}"
            git url: repoUrl
        }

        stage('Build Docker Image') {
            echo "Building image ${params.DOCKER_IMAGE_NAME}:${params.DOCKER_IMAGE_TAG}"
            sh "docker build -t ${params.DOCKER_IMAGE_NAME}:${params.DOCKER_IMAGE_TAG} ."
        }

        stage('Push to Docker Hub') {
            withCredentials([usernamePassword(
                credentialsId: 'dockerhub-creds-id',
                usernameVariable: 'DOCKER_USER',
                passwordVariable: 'DOCKER_PASS'
            )]) {
                sh """
                    echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    docker tag ${params.DOCKER_IMAGE_NAME}:${params.DOCKER_IMAGE_TAG} ${DOCKER_USER}/${params.DOCKER_IMAGE_NAME}:${params.DOCKER_IMAGE_TAG}
                    docker push ${DOCKER_USER}/${params.DOCKER_IMAGE_NAME}:${params.DOCKER_IMAGE_TAG}
                    docker logout
                """
            }
        }

        stage('Cleanup') {
            echo "Removing local image..."
            sh "docker rmi ${params.DOCKER_IMAGE_NAME}:${params.DOCKER_IMAGE_TAG} || true"
        }
    }
}
