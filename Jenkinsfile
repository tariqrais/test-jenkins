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

        stage('Deploy App onto EC2') {
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: 'ec2-ssh-key', keyFileVariable: 'SSH_KEY')]) {
                    script {
                        def ec2_ip = '54.145.201.67'  // Your EC2 public IP
                        def ec2_user = 'ubuntu'
                        def key_path = "%SSH_KEY%"  // Using the SSH key from Jenkins securely
                        // Use SSH to connect to the EC2 instance and deploy the application
                        def command = '''
                            "C:\\Windows\\System32\\OpenSSH\\ssh.exe -i \"%SSH_KEY%\" -o StrictHostKeyChecking=no ${ec2_user}@${ec2_ip} " +
                            "\"docker pull tariqdoc/flask-app:latest && docker run -d --name flask-app -p 8081:8081 tariqdoc/flask-app:latest\""

                        '''
                        bat command
                   }
                }
            }
        }
     }
 }

