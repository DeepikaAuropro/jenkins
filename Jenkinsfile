pipeline {
    agent any 
    
    stages{
        stage("Clone Code"){
            steps {
                echo "Cloning the code"
                git url:"https://github.com/hyndavi03/jenkins-sampleprj.git", branch: "main"
            }
        }
        
    }
}
