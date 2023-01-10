node {
    def myapp

    stage('Clone repository') {
        //git 'https://github.com/papemamadou91/simple-flask-app.git'
        checkout scm
        }

    stage('Build image') {
        /* This builds the actual image; synonymous to
         * docker build on the command line */
        myapp = docker.build("simple-flask-app")
    }

    stage('Test image') {
        /* Ideally, we would run a test framework against our image.
         * For this example, we're using a Volkswagen-type approach ;-) */

        myapp.inside {
            sh 'python3 test.py'
        }
    }

}
