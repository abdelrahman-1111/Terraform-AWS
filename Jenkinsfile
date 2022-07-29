pipeline {
    agent any
        stages {
            stage('terraform init') {
                steps {
                    withAWS(credentials: 'aws_credential', region: 'us-east-1'){
                    sh 'terraform init -upgrade -no-color'}
                }
            }
            // stage('terraform destroy') {
            //     steps {
            //         withAWS(credentials: 'aws_credential', region: 'us-east-1'){
            //         sh 'terraform destroy --auto-approve -no-color'}
            //     }
            // }
            stage('terraform plan') {
                steps {
                    withAWS(credentials: 'aws_credential', region: 'us-east-1'){
                    sh 'terraform plan -no-color '}
                    }
                }

            stage('terraform apply') {
                steps {
                    withAWS(credentials: 'aws_credential', region: 'us-east-1'){
                    sh 'terraform apply --auto-approve -no-color '}
                    }
            }
            stage('configure ec2 slave ') {
                steps {
                    withAWS(credentials: 'aws_credential', region: 'us-east-1'){
                    sh 'ansible-playbook -i /var/jenkins_home/hosts slave.yaml  '}
                }
            }
            stage('build the application'){
                steps {
                    agent { label 'slave-vm' }
                        sh '''
                            sudo chmod 666 /var/run/docker.sock
                            usermod -aG docker ubuntu
                            cd ./Nodeapp
                            docker build -f dockerfile -t nodejsapp .
                        '''    
                    }
                }
            stage('deploy the application'){
                steps {
                    agent { label 'slave-vm' }
                        sh '''
                            docker run -itd -p 3000:3000 -e RDS_HOSTNAME='admin' -e RDS_USERNAME='admin' -e RDS_PASSWORD='admin' -e RDS_PORT='3000' nodejsapp
                        '''
                    }
                }
            }
    }


