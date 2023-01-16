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
            myapp.push("${env.BUILD_NUMBER}")
            myapp.push("latest")
        }
    }
 
    stage('Deploy to PREPROD') {
        /* Deploy a container for PREPROD */
        myapp.image(myimage).run('--name preprod -d -p 5000:5000')   
      	//    sh 'docker run --name preprod -d -p 5000:5000 papemamadou/simple-flask-app:latest'
    }
}
