pipeline {
    agent any 

    environment {    
        DOCKERHUB_CREDENTIALS = credentials('dockerhub')
    } 
    
    stages {
        stage('SCM Checkout') {
            steps {
                // Get some code from a GitHub repository
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/DevopsEasy/star-agile.git'
            }
        }
        stage('Maven Build') {
            steps {
                // Run Maven on a Unix agent.
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
            }
        }
        stage("Docker build") {
            steps {
                sh 'docker version'
                sh "docker build -t devopseasy/insuranceapp:${BUILD_NUMBER} ."
                sh 'docker image list'
                sh "docker tag devopseasy/insuranceapp:${BUILD_NUMBER} devopseasy/insuranceapp:v1"
            }
        }
        stage('Login to Docker Hub') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }
        stage('Approve - push Image to Docker Hub') {
            steps {
                //----------------send an approval prompt-------------
                script {
                   env.APPROVED_DEPLOY = input message: 'User input required. Choose "yes" to approve or "Abort" to reject', ok: 'Yes', submitterParameter: 'APPROVER'
                }
                //-----------------end approval prompt------------
            }
        }
        stage('Push to Docker Hub') {
            steps {
                sh "docker push devopseasy/insuranceapp:v1"
            }
        }
     
    }
}
