def DOCKER_USERNAME = ""
pipeline{
    agent  any

    parameters {
        string(defaultValue: 'omarabdelwahabmb@gmail.com', description: 'Please, Enter Email to receive notifications:', name: 'Email')
        credentials(name: 'DOCKER_CREDENTIALS', description: 'Please, choose docker hub credentials:', required: true)
        string(description: 'Please, Enter hostname of instance:', name: 'hostname')
        string(description: 'Please, Enter Private key path:', name: 'key')
        booleanParam(name: 'Verify_Key_Fingerprint', defaultValue: true, description: 'Verify key fingerprint. Don\'t disable it. Use it for initial connection.')
    }

    stages{
        stage ('Parameter Validation'){
            steps{
                script{
                    if (params.Email == "" || params.DOCKER_USERNAME == "" || params.DOCKER_PASSWORD == "" || params.hostname == "" || params.key == "") {
                        error("One or more fields are empty!")
                    }
                    withCredentials([usernamePassword(credentialsId: params.DOCKER_CREDENTIALS, usernameVariable: 'DOCKER_USERNAME1', passwordVariable: 'DOCKER_PASSWORD')]) {
                        DOCKER_USERNAME = DOCKER_USERNAME1
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
                      docker build -t ${DOCKER_USERNAME}/vote:1 .   
                   """  
                sh """cd worker
                      docker build -t ${DOCKER_USERNAME}/worker:1 .
                   """ 
                sh """cd result
                      docker build -t ${DOCKER_USERNAME}/result:1 .
                   """ 
            }
        }

        stage('Docker Login'){
            steps{
                script{
                    withCredentials([usernamePassword(credentialsId: params.DOCKER_CREDENTIALS, usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {  
                        sh 'docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD'  
                    }
                }
            }
        }
        stage('Push Images'){
            steps{
                sh """ docker push ${DOCKER_USERNAME}/result:1
                       docker push ${DOCKER_USERNAME}/worker:1
                       docker push ${DOCKER_USERNAME}/vote:1
                   """           
            }
        }

        stage('Create Inventory'){
            steps{
                    sh """ 
                        mkdir -p inventory
                        echo "[ec2]" > inventory/hosts.ini
                        echo "${params.hostname} ansible_user=ec2-user ansible_ssh_private_key_file='${params.key}'" >> inventory/hosts.ini
                        sed -i '+s+pull .*/+pull ${DOCKER_USERNAME}/+g' prod-playbook.yml
                        sed -i '+s+image: .*/+image: ${DOCKER_USERNAME}/+g' docker-compose.yml
                    """
            }
        }
        

        stage('deploy with ansible'){
            steps{
                script {
                       if (!params.Verify_Key_Fingerprint) {
                            sh "sudo ssh-keyscan -H ${params.hostname} >> ~/.ssh/known_hosts"
                        }
                        sh "ansible-playbook -i inventory/hosts.ini prod-playbook.yml"
                }
            }
        }
        
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