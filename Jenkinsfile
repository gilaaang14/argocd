pipeline {
    agent any
    environment {
        DOCKER_REGISTRY = "docker.io/gilaaang14/"
        APP_NAME = "springboot"
        DEPLOY_PRODUCTION = "prod"
        IMAGE_TAG = "${BUILD_NUMBER}"
        IMAGE_NAME = "${DOCKER_REGISTRY}" + "/" + "${APP_NAME}"
        }
    stages {
        stage('docker build prodaction') {
            steps {
                sh "mvn clean"
                sh "mvn package"
                sh "docker build --pull --no-cache -t ${IMAGE_NAME}:${DEPLOY_PRODUCTION}.${IMAGE_TAG} ."
            }
        }
        stage('Docker push staging & Updating Kubernetes deployment file prodaction'){
            steps {
                script{
                    sh """
                    docker push ${IMAGE_NAME}:${DEPLOY_PRODUCTION}.${IMAGE_TAG}
                    cd ~/gitops/springboot
                    git pull https://gilaaang14:$secret@github.com/gilaaang14/gilang-test-devops-mandiri.git master
                    cat deployment-springboot.yaml
                    sed -i 's/${APP_NAME}:.*/${APP_NAME}:${DEPLOY_PRODUCTION}.${IMAGE_TAG}/g' deployment-springboot.yaml 
                    cat deployment-springboot.yaml
                    git add .
                    git commit -m 'update production docker tag ${IMAGE_TAG}'
                    git push https://gilaaang14:$secret@github.com/gilaaang14/gilang-test-devops-mandiri.git --all
                    docker rmi ${DOCKER_REGISTRY}/${APP_NAME}:${DEPLOY_PRODUCTION}.${IMAGE_TAG} """

            }
         }
    }
 }
