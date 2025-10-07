pipeline{
    agent {
        label 'python'
    }
    options {
        buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '1', numToKeepStr: '2')
    }
    environment{
        dockerhub=credentials('dockerhub')
        awscred=credentials('awscred')
    }
    stages{
        stage('code checkout'){
            steps{
                // sh 'sudo yum install git -y'
                sh 'git --version'
                checkout changelog: false, poll: false, scm: scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/choudharyaniruddhacert-bit/python-project-jenkins.git']])
            }
        }
        stage('build'){
            steps{
                sh 'sudo yum install pip -y'
                sh 'pip install -r requirements.txt'
            }
        }
        stage('docker install'){
            steps{
                sh 'sudo yum install docker -y'
                sh 'sudo systemctl start docker'
                sh 'sudo systemctl enable docker'
                sh 'sudo chmod 777 /var/run/docker.sock'
                sh 'docker -v'
            }
        }
        stage('docker build'){
            steps{
                sh 'docker build -t aniruddhaprojectes/python-project-jenkins:latest .'
            }
        }
        stage('docker push'){
            steps{
                sh 'echo $dockerhub_PSW | docker login -u $dockerhub_USR --password-stdin'
                sh 'docker push aniruddhaprojectes/python-project-jenkins:latest'
            }
        }
        stage('aws connection check'){
            steps{
                sh 'aws --version'
            }
        }
        stage('kubernetes connection check'){
            steps{
                sh 'kubectl version --client'
                sh 'aws eks --region us-east-2 update-kubeconfig --name java-project-eks-cluster'
                sh 'kubectl config current-context'
                sh 'kubectl version'
            }
        }
        stage('deploy to kubernetes'){
            steps{
                sh 'kubectl apply -f Application.yaml'
                sh 'sleep 30'
                sh 'kubectl get pods -o wide'
            }
        }

    }

}