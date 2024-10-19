pipeline{
    agent  any

    stages{
        stage('clone repo'){
            steps{
                git ('git@github.com:omarabdelwahabmb/depi-recommendation.git')
            }
        }    
        stage('build-docker-images'){
            steps{
                sh '''cd /home/yat/Desktop/omar-final/depi-recommendation/vote
                      docker build -t 1998shehab/vote:01 .   
                   '''  
                sh '''cd /home/yat/Desktop/omar-final/depi-recommendation/worker
                      docker build -t 1998shehab/worker:01 .
                   ''' 
                sh '''cd /home/yat/Desktop/omar-final/depi-recommendation/result
                      docker build -t 1998shehab/result:01 .
                   ''' 
            }
        }
        stage('test images'){
            steps{
                sh 'docker images'
            }
        }
        
        stage('docker login'){
            steps{
                script{
                    withCredentials([usernamePassword(credentialsId: 'docker-credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {  
                        sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin'  
                    }
                }
            }
        }
        stage('push images'){
            steps{
                sh ''' docker push 1998shehab/result:01
                       docker push 1998shehab/worker:01
                       docker push 1998shehab/vote:01
                   '''           
            }
        }
        
        stage('deploy with ansible'){
            steps{
                sh ''' cd /home/yat/Desktop/omar-final
                       ansible-playbook -i ec2.ini ec2.yml
                   '''
            }
        }
        
    }
    post {
        success {
            mail to: "${mail_user}",
                 subject: "Succeeded pipeline: ${currentBuild.fullDisplayName}",
                 body: "Well done, Group 2!"
        }        

        failure {
            mail to: "${mail_user}",
                 subject: "failed pipeline: ${currentBuild.fullDisplayName}",
                 body: "try again"
        }
    }        
}