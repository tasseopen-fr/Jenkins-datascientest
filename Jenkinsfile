pipeline {
    environment { // Declaration of environment variables
        DOCKER_ID = "tasseopen" // replace this with your docker-id
        DOCKER_IMAGE = "datascientestapi"
        DOCKER_TAG = "v.${BUILD_ID}.0" // we will tag our images with the current build in order to increment the value by 1 with each new build
    }
    agent any // Jenkins will be able to select all available agents
    stages {
        stage(' Docker Build'){ // docker build image stage
            steps {
                script {
                sh '''
                docker rm -f jenkins
                docker build -t $DOCKER_ID/$DOCKER_IMAGE:$DOCKER_TAG .
                sleep 6
                '''
                }
            }
        }
        stage('Docker run'){ // run container from our builded image
                steps {
                    script {
                    sh '''
                    docker run -d -p 80:80 --name jenkins $DOCKER_ID/$DOCKER_IMAGE:$DOCKER_TAG
                    sleep 10
                    '''
                    }
                }
            }

        stage('Test Acceptance'){ // we launch the curl command to validate that the container responds to the request
            steps {
                    script {
                    sh '''
                    curl localhost
                    '''
                    }
            }

        }
        stage('Docker Push'){ //we pass the built image to our docker hub account
            environment
            {
                DOCKER_PASS = credentials("DOCKER_HUB_PASS") // we retrieve  docker password from secret text called docker_hub_pass saved on jenkins
            }

            steps {

                script {
                sh '''
                docker login -u $DOCKER_ID -p $DOCKER_PASS
                docker push $DOCKER_ID/$DOCKER_IMAGE:$DOCKER_TAG
                '''
                }
            }

        }
    }
}
