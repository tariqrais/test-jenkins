pipeline{
    agent any
        environment {
        KUBECONFIG = credentials('mykubeconfig') // Using the credential ID
    }
    
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
                bat 'docker tag flask-app tariqdoc/flask-app:latest'
            }
        } 
        stage('Docker Push') {
            steps {

                withCredentials([usernamePassword(credentialsId: 'dockerHub' , passwordVariable:'dockerHubPassword' , usernameVariable: 'dockerHubUser')]){
                    bat "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
                    bat 'docker push tariqdoc/flask-app:latest'
                }
            }
        }    

        stage('deploy to KIND'){
            steps {
               script {
                    // Apply the deployment
                    bat 'kubectl apply -f deployment.yaml'
            
            // Wait for the deployment to be ready
                    def maxRetries = 30
                    def delay = 10  // seconds
                    def retries = 0
            
                    while (true) {
                    def status = bat(script: 'kubectl rollout status deployment/flask-app', returnStatus: true)
                    if (status == 0) {
                    echo 'Deployment is ready!'
                    break
                    } else {
                    if (retries >= maxRetries) {
                        error 'Deployment failed to become ready within the time limit.'
                    }
                    echo "Waiting for deployment to be ready... (${retries + 1}/${maxRetries})"
                    sleep delay
                    retries++
                        }
                    }
     
                    // Apply the service configuration only after the pod is ready
                    bat 'kubectl apply -f service.yaml'
            
                    // Port forward (if needed)
                    bat 'kubectl port-forward service/flask-app-service 8082:8081'
                        }
              }
        }

    }
}   
