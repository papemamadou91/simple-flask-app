node {
    def myapp
    def myimage = "papemamadou/simple-flask-app"

    stage('Clone repository') {
        checkout scm
        }

    stage('Build image Docker') {
        /* This builds the actual image; synonymous to
         * docker build on the command line */
        
        myapp = docker.build(myimage)
    }

    stage('Test APP') {
        /* Test our python APP*/

        myapp.inside {
            sh 'python3 test.py'
        }
    }
    
    stage('PUBLISH') {
        /* Publish image to DockerHub */
        //def customImage = docker.build("simple-flask-app:${env.BUILD_ID}")
        
        docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-credentials') {
            //myapp.push("${env.BUILD_NUMBER}")
            myapp.push("latest")
        }
    }
 
    stage('Deploy to PREPROD') {
        /* Deploy a container for PREPROD */
        sh 'docker stop preprod && docker rm preprod'
        myapp.run(' --restart always --name preprod -p 5000:5000')   
      	//    sh 'docker run --restart always --name preprod -d -p 5000:5000 papemamadou/simple-flask-app:latest'
    }
 
    stage('TEST PREPROD') {
        sh 'sudo docker exec -it preprod sh -c "python3 test.py" | grep OK'
    }
    
    stage('Deploy to PROD') {
        /* Deploy a container for PROD */
        sh 'docker stop prod && docker rm prod'
        myapp.run('--restart always --name prod -p 5000:5000')   
    }
}
