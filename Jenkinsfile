node {
    def myapp

    stage('Clone repository') {
        checkout scm
        }

    stage('Build image Docker') {
        /* This builds the actual image; synonymous to
         * docker build on the command line */
        
        myapp = docker.build("simple-flask-app")
    }

    stage('Test APP') {
        /* Test our python APP*/

        myapp.inside {
            sh 'python3 test.py'
        }
    }
    
    stage('PUBLISH') {
        /* Publish image to DockerHub */
        
        docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-credentials') {
            myapp.push("${env.BUILD_NUMBER}")
            myapp.push("latest")
        }
    }
}
