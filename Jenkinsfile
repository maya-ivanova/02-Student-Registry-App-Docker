node {
    stage('Checkout Code') {
        git branch: 'main', url: 'https://github.com/maya-ivanova/02-Student-Registry-App-Docker.git'
    }

    stage('Set Up Node.js') {
        def nodeHome = tool name: "NodeJS-23"
        env.PATH = "${nodeHome}\\bin;${env.PATH}" // Corrected for Windows
    }

    stage('Verify Node.js & npm') { // Fixed duplicate stage name
        bat 'node -v'  // Verify Node.js version
        bat 'npm -v'   // Verify npm version
    }

    stage('Install Dependencies') {
        bat 'npm install'
    }

    stage('Run Tests') {
        bat 'npm run test'
    }

    stage('Build Docker Image') {
        bat 'docker build -t %USER%/student-registry:%BUILD_NUMBER% .'
    }

    stage('Login to Docker Hub') {
        withCredentials([string(credentialsId: 'docker-hub-token', variable: 'DOCKER_PASS')]) {
            bat 'echo %DOCKER_PASS% | docker login -u %USER% --password-stdin'
        }
    }

    stage('Push Docker Image') {
        bat '''
        docker push %USER%/student-registry:%BUILD_NUMBER%
        docker tag %USER%/student-registry:%BUILD_NUMBER% %USER%/student-registry:latest
        docker push %USER%/student-registry:latest
        '''
    }

    stage('Deploy to Server') {
        bat '''
        docker pull %USER%/student-registry:latest
        docker-compose -f docker-compose.yml up -d
        '''
    }
}
