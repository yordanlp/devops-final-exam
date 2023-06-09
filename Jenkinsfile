pipeline {
    agent any


    stages {

        stage('build and push docker image'){
            steps{
                sh 'docker build . --tag ttl.sh/yordan-node-app:1h'
                sh 'docker push ttl.sh/yordan-node-app:1h'
            }
        }

        stage('deploy to staging'){
            steps{
                withCredentials([sshUserPrivateKey(credentialsId: 'target-ssh-credentials', keyFileVariable: 'keyFile', usernameVariable: 'userName')]) {
                    sh "ssh-keyscan 192.168.105.3 > ~/.ssh/known_hosts"
                    sh "ssh -l ${userName} -i ${keyFile} 192.168.105.3 -C docker pull ttl.sh/yordan-node-app:1h"
                    sh "ssh -l ${userName} -i ${keyFile} 192.168.105.3 -C docker rm --force yordan-node-app"
                    sh "ssh -l ${userName} -i ${keyFile} 192.168.105.3 -C docker run --detach --publish 5555:5555 --name yordan-node-app ttl.sh/yordan-node-app:1h"
                }
            }
        }

        // stage('deploy to aws ec2 instance'){
        //     steps{
        //         withCredentials([sshUserPrivateKey(credentialsId: 'target-ssh-credentials', keyFileVariable: 'keyFile', usernameVariable: 'userName')]) {
        //             sh "ssh-keyscan 13.48.149.77 > ~/.ssh/known_hosts"

        //             sh "ssh -l ubuntu -i ${keyFile} 13.48.149.77 -C sudo systemctl stop myapp"
        //             sh "scp -i ${keyFile} main ubuntu@13.48.149.77:"
        //             sh "scp -i ${keyFile} myapp.service ubuntu@13.48.149.77:"
        //             sh "ssh -l ubuntu -i ${keyFile} 13.48.149.77 -C sudo mv myapp.service /etc/systemd/system"
        //             sh "ssh -l ubuntu -i ${keyFile} 13.48.149.77 -C sudo systemctl daemon-reload"
        //             sh "ssh -l ubuntu -i ${keyFile} 13.48.149.77 -C sudo systemctl enable myapp"
        //             sh "ssh -l ubuntu -i ${keyFile} 13.48.149.77 -C sudo systemctl start myapp"
        //         }
        //     }
        // }

        // stage('health check'){
        //     steps{
        //         sh "curl -s http://192.168.105.4:5555/api"
        //     }
        // }
    }
}