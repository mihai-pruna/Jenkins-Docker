pipeline 
{ 
    agent any
environment {
        DOCKER_ID = credentials('DOCKER_ID')
        DOCKER_PASSWORD = credentials('DOCKER_PASSWORD')
}

stages {
    stage('Checkout main branch') {
        steps {
           checkout scm  
        }
    }
    
    stage('Build') {
        steps {
            sh 'docker build -t node-app .'
        }
    }
    
    stage('Test') {
        steps {
            sh '''
                  if [ $(docker ps -q -f name=node-container) ]; then
                        docker stop node-container
                        docker rm node-container
                  else
                        docker run --name node-container -p 3000:3000 -d node-app &&
                        status_code=$(curl -s -o /dev/null -w "%{http_code}" localhost:8080) || $?
                        docker stop node-container && docker rm -f node-container
                 fi
               '''
            }
        }
    
    stage('Push to DockerHub') {
        steps {
                   sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_ID --password-stdin'
                   sh "docker tag node-app ${DOCKER_ID}/node-app"
                   sh "docker push ${DOCKER_ID}/node-app" 
                }
            }
        }
    }

