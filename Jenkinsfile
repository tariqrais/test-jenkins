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
                sh 'pip install -r requirements.txt'
                }
            }
        stage('Build docker') {
             steps {
                sh 'docker build -t flask-app .' 
                sh 'docker tag flask-app tariqdoc/flask-app:latest'
            }
        } 
        stage('Docker Push') {
            steps {

                withCredentials([usernamePassword(credentialsId: 'dockerhub' , passwordVariable:'dockerhubPassword' , usernameVariable: 'dockerhubUser')]){
                    sh "docker login -u ${env.dockerhubUser} -p ${env.dockerhubPassword}"
                    sh 'docker push tariqdoc/flask-app:latest'
                }
            }
        }    

        stage('Deploy App onto EC2') {
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: 'ec2-ssh-key', keyFileVariable: 'SSH_KEY')]) {
                    script {
                        def ec2_ip = '54.159.27.207'  // Your EC2 public IP
                        def ec2_user = 'ubuntu'
                        // def key_path = "${SSH_KEY}"  // Using the SSH key from Jenkins securely
                        // Use SSH to connect to the EC2 instance and deploy the application
                        def command = '''
                            ssh -i /var/lib/jenkins/.ssh/new-key.pem -o StrictHostKeyChecking=no ubuntu@ec2-54-159-27-207.compute-1.amazonaws.com "
                            docker pull tariqdoc/flask-app:latest &&
                            docker stop flask-app || true &&
                            docker rm flask-app || true &&
                            docker run -d --name flask-app -p 8081:8081 tariqdoc/flask-app:latest"

                        '''
                        sh command
                   }
                }
            }
        }
     }
 }

