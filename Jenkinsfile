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
        // stage('Docker Push') {
        //     steps {

        //         withCredentials([usernamePassword(credentialsId: 'dockerHub' , passwordVariable:'dockerHubPassword' , usernameVariable: 'dockerHubUser')]){
        //             bat "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
        //             bat 'docker push tariqdoc/flask-app:latest'
        //         }
        //     }
        // }    

        stage('Deploy app onto EC2'){
            steps {
                    // Use SSH to connect to the EC2 instance and deploy the application
                    sshagent(['aws-creds']) {
                    bat '''
                        ssh -o StrictHostKeyChecking=no ubuntu@35.173.122.237 << EOF
                            docker pull tariqdoc/flask-app:latest
                            docker stop flask-app || true
                            docker rm flask-app || true
                            docker run -d --name flask-app -p 80:8081 tariqdoc/flask-app:latest
                        EOF
                    '''
                }
            }
        }
     }
 }

