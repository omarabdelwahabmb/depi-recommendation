pipeline{
    agent  any

    parameters {
        string(defaultValue: 'omarabdelwahabmb@gmail.com', description: 'Please, Enter Email to receive notifications:', name: 'Email')
        string(defaultValue: 'omarabdelwahabmb', description: 'Please, dockerhub username:', name: 'DOCKER_USERNAME')
        password(description: 'Please, dockerhub password:', name: 'DOCKER_PASSWORD')
        string(defaultValue: '35.92.134.21', description: 'Please, Enter hostname of instance:', name: 'hostname')
        string(defaultValue: '/home/omar/Eng/Courses/AWS - Sprints/keys/labsuser.pem', description: 'Please, Enter Private key path:', name: 'key')
        booleanParam(name: 'Verify_Key_Fingerprint', defaultValue: true, description: 'Verify key fingerprint. Don\'t disable it. Use it for initial connection.')
    }

    stages{
        stage ('Parameter Validation'){
            steps{
                script{
                    if (params.Email == "" || params.DOCKER_USERNAME == "" || params.DOCKER_PASSWORD == "" || params.hostname == "" || params.key == "") {
                        error("One or more fields are empty!")
                    }
                }
            }
        }
        stage('Clone Repo'){
            steps{
                git(url: "https://github.com/omarabdelwahabmb/depi-recommendation.git", branch: 'main')
            }
        }    
        stage('Build Docker Images'){
            steps{
                sh """cd vote
                      docker build -t ${params.DOCKER_USERNAME}/vote:1 .   
                   """  
                sh """cd worker
                      docker build -t ${params.DOCKER_USERNAME}/worker:1 .
                   """ 
                sh """cd result
                      docker build -t ${params.DOCKER_USERNAME}/result:1 .
                   """ 
            }
        }

        stage('Docker Login'){
            steps{
                script{
                    sh "echo ${params.DOCKER_PASSWORD} | docker login -u ${params.DOCKER_USERNAME} --password-stdin "
                }
            }
        }
        stage('Push Images'){
            steps{
                sh """ docker push ${params.DOCKER_USERNAME}/result:1
                       docker push ${params.DOCKER_USERNAME}/worker:1
                       docker push ${params.DOCKER_USERNAME}/vote:1
                   """           
            }
        }

        stage('Create Inventory'){
            steps{
                sh """ 
                    mkdir inventory
                    echo "[ec2]" > hosts.ini
                    echo "${params.hostname} ansible_user=ec2-user ansible_ssh_private_key_file=${params.key}" >> hosts.ini
                    sed -i '+s+pull .*/+pull ${params.DOCKER_USERNAME}/+g' prod-playbook.yml
                    sed -i '+s+image: .*/+image: ${params.DOCKER_USERNAME}/+g' docker-compose.yml
                    if (params.Verify_Key_Fingerprint == "false") {
                        sh "ssh-keyscan -H ${params.hostname} >> ~/.ssh/known_hosts"
                    }
                    sh "ansible-playbook -i hosts.ini prod-playbook.yml"
                   """
            }
        }
        

        /*stage('deploy with ansible'){
            steps{
                script {
                }
            }
        }*/
        
    }
    post {
        success {
            mail to: "${params.Email}",
                 subject: "Succeeded pipeline: ${currentBuild.fullDisplayName}",
                 body: "Well done, Group 2!"
        }        

        failure {
                script{
                    if (params.Email != "") {
                        mail to: "${params.Email}",
                            subject: "failed pipeline: ${currentBuild.fullDisplayName}",
                            body: "try again"
                    }
                }
        }
    }        
}