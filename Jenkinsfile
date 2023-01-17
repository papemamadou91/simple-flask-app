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
        /* Deploy a container for PREPROD (ensuring to stop and remove it first) */
        sh 'docker ps -f name=preprod -q | xargs --no-run-if-empty docker container stop'
        sh 'docker container ls -a -fname=^preprod$ -q | xargs -r docker container rm'
        myapp.run('--restart always --name preprod -p 5000:5000')   
    }
 
    stage('TEST PREPROD') {
        def TestResult = sh( script: 'docker exec -t preprod sh -c "python3 test.py" | grep OK', returnStdout: true ).trim()
        echo "${TestResult}"
    }
    
    
    stage('Deploy to PROD') {
        /* Deploy a container for PROD */
        //sh 'docker ps -aq --filter name=prod | grep -q . && docker stop prod && docker rm -fv prod'
        echo "${TestResult}"
        sh 'docker ps -f name=^prod$ -q | xargs --no-run-if-empty docker container stop'
        sh 'docker container ls -a -fname=prod -q | xargs -r docker container rm'
        myapp.run('--restart always --name prod -p 5001:5000')   
    }
}
