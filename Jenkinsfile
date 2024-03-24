pipeline{
    agent any


    parameters{

    booleanParam(name:'check',defaultValue:true, description:"this is for checking purpose")

    }

    environment {
        // Define the version number using the Jenkins BUILD_NUMBER
        VERSION = "${BUILD_NUMBER}"
        IMAGE_NAME="python"
        DOCKER_IMAGE_TAG = "${IMAGE_NAME}:${VERSION}"
        DOCKER_HUB_REPO = "venkatahyndavi/python"
        DOCKER_HUB_TAG = "${DOCKER_HUB_REPO}:${VERSION}"
    }

    stages {
        stage('Build Dockerfile') {
            steps {
                script {
                    // Define the placeholder in the Dockerfile
                    def placeholder = "##VERSION_PLACEHOLDER##"

                    // Read the Dockerfile content
                    def dockerfileContent = readFile("Dockerfile")

                    // Replace the placeholder with the version number
                    def updatedDockerfileContent = dockerfileContent.replaceAll(placeholder, VERSION)

                    // Write the updated content back to the Dockerfile
                    writeFile(file: "Dockerfile", text: updatedDockerfileContent)
                }
            }
        }
        

        stage("docker login"){

            when{
                expression{
                    params.check
                }
            }

           steps{
                withCredentials([usernamePassword(credentialsId:'python-id' , usernameVariable:'user' , passwordVariable:'pass')]){
                         sh " docker login -u $user -p $pass "



                }



                }
            }

    
        stage("Image push to Docker hub "){

           steps{
                sh "docker build -t ${DOCKER_IMAGE_TAG} ."
                sh " docker tag ${DOCKER_IMAGE_TAG} ${DOCKER_HUB_TAG}"
                sh " docker push  ${DOCKER_HUB_TAG} "
            }
        }
        
        stage("Scan"){
            
            steps {
                sh "trivy image ${DOCKER_IMAGE_TAG}"
                
            }
        }
        
        stage("Deploy"){
           steps {
              echo "Preparing environment variables"
              sh "echo DOCKER_IMAGE_FULL=${DOCKER_HUB_TAG} > .env"
              echo "Deploying the container"
              sh "docker-compose down && docker-compose up -d"
           }
        }

    }
}