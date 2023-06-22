node {
    def application = 'springbootapp'
    def dockerhubaccountid = 'vmovahed'

    stage('Clone Repository') {
        checkout scm
    }

    stage('Stop Docker Containers') {
        sh('(docker container rm -f $(docker ps -a -q)) || true')
    }

    stage('Running my Unit Tet') {
        sh('./mvnw test')
    }

    stage('Building the Artifact') {
        sh('./mvnw clean package')
    }

    stage('Build Image') {
        app = docker.build("${dockerhubaccountid}/${application}:${BUILD_NUMBER}")
    }

    stage('Push Image') {
        withDockerRegistry([ credentialsId: 'dockerHub', url: '' ]) {
            app.push()
            app.push('latest')
        }
    }

    stage('Deploy') {
        sh("docker run -d -p 81:8080 -v /var/log/:/var/log/ ${dockerhubaccountid}/${application}:${BUILD_NUMBER}")
    }

    stage('Remove Old Images') {
        sh("docker rmi ${dockerhubaccountid}/${application}:latest -f")
    }
}
