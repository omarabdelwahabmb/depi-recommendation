pipeline{
    agent  any

    parameters {
        string(defaultValue: 'omarabdelwahabmb@gmail.com', description: 'Please, Enter Email to receive notifications:', name: 'Email')
        string(defaultValue: '1998shehab', description: 'Please, dockerhub username:', name: 'DOCKER_USERNAME')
        password(description: 'Please, dockerhub password:', name: 'DOCKER_PASSWORD')
        string(defaultValue: '1998shehab', description: 'Please, enter dockerhub repo', name: 'repo')
        string(description: 'Please, Enter hostname of instance:', name: 'hostname')
        string(description: 'Please, Enter Private key path:', name: 'key')
        booleanParam(name: 'Verify Key Fingerprint', defaultValue: true, description: 'Verify key fingerprint. Don\'t disable it. Use it for initial connection.')
    }

    stages{
        stage ('Parameter Validation'){
            if (${params.password} == "") {
                error("Empty Password!")
            }
        }
        stage('Clone Repo'){
            steps{
                git("https://github.com/omarabdelwahabmb/${params.repo}.git")
            }
        }    
        stage('Build Docker Images'){
            steps{
                sh """cd /home/yat/Desktop/omar-final/${params.repo}/vote
                      docker build -t ${params.DOCKER_USER}/vote:1 .   
                   """  
                sh """cd /home/yat/Desktop/omar-final/${params.repo}/worker
                      docker build -t ${params.DOCKER_USER}/worker:1 .
                   """ 
                sh """cd /home/yat/Desktop/omar-final/${params.repo}/result
                      docker build -t ${params.DOCKER_USER}/result:1 .
                   """ 
            }
        }

        stage('Docker Login'){
            steps{
                script{
                    sh "echo ${params.DOCKER_PASSWORD} | docker login -u ${params.DOCKER_USERNAME} --password-stdin"
                }
            }
        }
        stage('Push Images'){
            steps{
                sh """ docker push ${params.DOCKER_USER}/result:1
                       docker push ${params.DOCKER_USER}/worker:1
                       docker push ${params.DOCKER_USER}/vote:1
                   """           
            }
        }

        stage('Create Inventory'){
            steps{
                sh """ 
                    mkdir inventory
                    echo "[ec2]" > inventory/hosts.ini
                    echo "${params.hostname} ansible_user=ec2-user ansible_ssh_private_key_file=${params.key}" >> inventory/hosts.ini
                    sed -i '+s+pull .*/+pull ${params.repo}/+g' prod-playbook.yml
                    sed -i '+s+image: .*/+image: ${params.repo}/+g' prod-playbook.yml
                   """
            }
        }
        

        stage('deploy with ansible'){
            steps{
                sh """ 
                       ssh-keyscan -H ${params.hostname} >> ~/.ssh/known_hosts
                       ansible-playbook -i inventory/hosts.ini -e "repo=${params.params.repo}" prod-playbook.yml
                   """
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
            mail to: "${params.Email}",
                 subject: "failed pipeline: ${currentBuild.fullDisplayName}",
                 body: "try again"
        }
    }        
}