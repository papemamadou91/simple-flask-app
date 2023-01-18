node {
    def myapp
    def myimage = "papemamadou/simple-flask-app"
    def HealthCheck

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
        sh 'docker ps -f name=^preprod$ -q | xargs --no-run-if-empty docker container stop'
        sh 'docker container ls -a -fname=^preprod$ -q | xargs -r docker container rm'
        myapp.run('--restart always --name preprod -p 5000:5000')   
    }
 
    stage('TEST PREPROD') {
        //TestResult = sh( script: 'docker exec -t preprod sh -c "python3 test.py" | grep OK', returnStdout: true ).trim()
        HealthCheck = sh( script: 'curl -Is  https://5000-port-f726586041fb48ac.labs.kodekloud.com/ | head -n 1 | awk '{print $2}'', returnStdout: true ).trim()
        echo "${HealthCheck}"
        
        if (HealthCheck.equals("200")) {
        } else {
          sh "echo ${HealthCheck} ; exit 1" // this fails the stage
       }
    }
    
    
    stage('Deploy to PROD') {
        /* Deploy a container for PROD */
        echo "${HealthCheck}"
        sh 'docker ps -f name=^prod$ -q | xargs --no-run-if-empty docker container stop'
        sh 'docker container ls -a -fname=^prod$ -q | xargs -r docker container rm'
        myapp.run('--restart always --name prod -p 5001:5000')   
    }
}
