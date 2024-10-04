pipeline{
    agent any
    //     environment {
    //     KUBECONFIG = credentials('mykubeconfig') // Using the credential ID
    // }
    
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

        stage('Deploy app onto EC2'){
            steps {
                     withCredentials([sshUserPrivateKey(credentialsId: 'ec2-ssh-key', keyFileVariable: 'SSH_KEY')]){
                    // Use SSH to connect to the EC2 instance and deploy the application
                    sshagent(['ec2-ssh-key']) {
                    powershell '''
                        ssh -i $env:SSH_KEY -o StrictHostKeyChecking=no ubuntu@35.173.122.237 `
                            "docker pull tariqdoc/flask-app:latest; `
                             docker stop flask-app || true; `
                             docker rm flask-app || true; `
                             docker run -d --name flask-app -p 8081:5000 tariqdoc/flask-app:latest"
                    '''
                    
                  }
                }
            }
        }
     }
 }

