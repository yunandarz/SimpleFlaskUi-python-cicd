def img
pipeline {
    environment {
        registry = "yunandar711/simple-flask-ui" // To push an image to Docker Hub, you must first name your local image using your Docker Hub username and the repository name that you created through Docker Hub on the web.
        registryCredential = 'dockerhub-yunandar711' // it's have to be same with ID credential name
        dockerImage = ''
    }
    agent any
    stages {

        stage('Build Image') {
            steps {
                script {
                    img = registry + ":${env.BUILD_ID}"
                    println ("${img}")
                    dockerImage = docker.build("${img}")
                }
            }
        }

        stage('Push To DockerHub') {
            steps {
                script {
                    docker.withRegistry( 'https://registry.hub.docker.com ', registryCredential ) {
                        dockerImage.push()
                    }
                }
            }
        }

        stage('Deploy to Server') {
            steps {
                script {
                    def stopcontainer = "docker stop ${JOB_NAME}"
                    def delcontName = "docker rm ${JOB_NAME}"
                    def delimages = 'docker image prune -a --force'
                    def drun = "docker run -d --name ${JOB_NAME} -p 5000:5000 ${img}"
                    println "${drun}"
                    sshagent(['docker-test']) {
                        sh returnStatus: true, script: "ssh -o StrictHostKeyChecking=no yunandar-app@103.117.56.235 ${stopcontainer} "
                        sh returnStatus: true, script: "ssh -o StrictHostKeyChecking=no yunandar-app@103.117.56.235 ${delcontName}"
                        sh returnStatus: true, script: "ssh -o StrictHostKeyChecking=no yunandar-app@103.117.56.235 ${delimages}"

                        sh "ssh -o StrictHostKeyChecking=no yunandar-app@103.117.56.235 ${drun}"
                    }
                }
            }
        }

        stage('Delete image in jenkins') {
            steps {
                sh 'docker image prune -a --force'
            }
        }

    }
}
