pipeline{
    agent any
    
    stages{
        stage("git checkout"){
            steps{
                // Clone the repository from GitHub
                git url:'https://github.com/tariqrais/test-jenkins.git', branch: 'main'
            }    
        }
        stage('Install Dependencies') {
            steps {
                // Install dependencies from requirements.txt
                bat 'pip install -r requirements.txt'
                }
            }
        stage('Build docker') {
             steps {

                bat 'docker build -t flask-app .' 
                bat 'docker tag flask-app tariqdoc/flask-app:%BUILD_NUMBER%'
            }
        } 
        stage('Docker Push') {
            steps {

                withCredentials([usernamePassword(credentialsId: 'dockerHub' , passwordVariable:'dockerHubPassword' , usernameVariable: 'dockerHubUser')]){
                    bat "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
                    bat 'docker push tariqdoc/flask-app:%BUILD_NUMBER%'
                }
            }
        }    

        stage('deploy app'){
            steps {
                bat 'kubectl apply -f deployment.yaml'

            }
        }
    }
}   
