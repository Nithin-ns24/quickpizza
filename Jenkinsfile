node {
    // Define variables
    def repoUrl = 'https://github.com/your-user/your-repo.git'
    def dockerImageName = 'your-image-name'
    def dockerTag = 'latest' // or use Git commit hash, build number, etc.

    stage('Checkout Code') {
        echo "Cloning repo..."
        git url: repoUrl
    }

    stage('Build Docker Image') {
        echo "Building Docker image..."
        sh "docker build -t ${dockerImageName}:${dockerTag} ."
    }

    stage('Push to Docker Hub') {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds-id', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
            sh """
                echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                docker tag ${dockerImageName}:${dockerTag} ${DOCKER_USER}/${dockerImageName}:${dockerTag}
                docker push ${DOCKER_USER}/${dockerImageName}:${dockerTag}
                docker logout
            """
        }
    }

    stage('Cleanup') {
        echo "Removing local image..."
        sh "docker rmi ${dockerImageName}:${dockerTag} || true"
    }
}
