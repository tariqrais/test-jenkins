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
            }
        } 
    }
}
