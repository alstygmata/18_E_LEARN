#!groovy
//  groovy Jenkinsfile
properties([disableConcurrentBuilds()])

pipeline  {
    agent { 
        label 'master'
        }
    options {
        buildDiscarder(logRotator(numToKeepStr: '10', artifactNumToKeepStr: '10'))
        timestamps()
    }
    stages {
        stage("Create docker image") {
            steps {
                echo 'Creating docker image ...'
                dir('.'){
                    sh "docker build --no-cache -t diogensynonsy/website  . "
                }
            }
        }
        stage("docker login") {
            steps {
                echo " ============== docker login =================="
                withCredentials([usernamePassword(credentialsId: 'DockerHub-Credentials', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh '''
                    docker login -u $USERNAME -p $PASSWORD
                    '''
                }
            }
        }
        
        stage("remove all containers and images") {
            steps {
                echo " ==============remove images and containers =================="
                sh '''
                /home/master/delete.sh
                '''
            }
        }
        
        stage("docker push") {
            steps {
                echo " ============== pushing image =================="
                sh '''
                docker push diogensynonsy/website:latest
                '''
            }
        }
     
        stage("docker run") {
            steps {
                echo " ============== start server =================="
                sh '''
                docker run -d --restart=always --name website -p 80:80 diogensynonsy/website
                '''
            }
        }
    }
}
