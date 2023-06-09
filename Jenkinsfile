pipeline {
    agent any


    stages {
        
        stage('installing npm dependencies'){
            steps{
                sh 'npm install'
            }
        }

        stage('run tests'){
            steps{
                sh 'node --test'
            }
        }

        stage('build and push docker image'){
            steps{
                sh 'chmod 777 package-lock.json'
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


        stage('health check staging'){
            steps{
                sleep 2
                sh "curl -s http://192.168.105.3:5555/api"
            }
        }

        stage('deploy to prod'){
            when {
                branch 'main'
            }
            steps{
                withCredentials([sshUserPrivateKey(credentialsId: 'target-ssh-credentials', keyFileVariable: 'keyFile', usernameVariable: 'userName')]) {
                    sh "ssh-keyscan 192.168.105.4 > ~/.ssh/known_hosts"
                    sh "ssh -l ${userName} -i ${keyFile} 192.168.105.4 -C docker pull ttl.sh/yordan-node-app:1h"
                    sh "ssh -l ${userName} -i ${keyFile} 192.168.105.4 -C docker rm --force yordan-node-app"
                    sh "ssh -l ${userName} -i ${keyFile} 192.168.105.4 -C docker run --detach --publish 5555:5555 --name yordan-node-app ttl.sh/yordan-node-app:1h"
                }
            }
        }

        stage('health check production'){
            when {
                branch 'main'
            }
            steps{
                sleep 2
                sh "curl -s http://192.168.105.4:5555/api"
            }
        }
    }
}