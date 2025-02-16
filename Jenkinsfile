node {
    stage('Checkout Code') {
        git branch: 'main', url: 'https://github.com/maya-ivanova/02-Student-Registry-App-Docker.git'
    }

    stage('Set Up Node.js') {
        def nodeHome = tool name: "NodeJS-23", type: "hudson.tools.NodeJSInstallation"
        env.PATH = "${nodeHome}/bin:${env.PATH}"
    }

    stage('Install Dependencies') {
        sh 'npm install'
    }

    stage('Run Tests') {
        sh 'npm test'
    }

    stage('Build Docker Image') {
        sh 'docker build -t $USER/student-registry:$BUILD_NUMBER .'
    }

    stage('Login to Docker Hub') {
        withCredentials([string(credentialsId: 'docker-hub-token', variable: 'DOCKER_PASS')]) {
            sh 'echo $DOCKER_PASS | docker login -u $USER --password-stdin'
        }
    }

    stage('Push Docker Image') {
        sh '''
        docker push $USER/student-registry:$BUILD_NUMBER
        docker tag $USER/student-registry:$BUILD_NUMBER $USER/student-registry:latest
        docker push $USER/student-registry:latest
        '''
    }

    stage('Deploy to Server') {
        sh '''
        docker pull $USER/student-registry:latest
        docker-compose -f docker-compose.yml up -d
        '''
    }
}
