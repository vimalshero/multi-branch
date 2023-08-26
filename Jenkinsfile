pipeline {
    agent any
    stages {
        stage ('Build') {
            steps {
                script{
                     sh 'npm install'
                }
            }
        }
        // stage ('SonarQube Analysis') {
        //     steps {
        //         script { 
        //             def scannerHome = tool 'sonarscanner4'
        //             withSonarQubeEnv('sonar-pro') {
        //                 sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=node.js-app"
        //             }
        //         }
        //     }
        // }
        stage('Docker Build Images') {
            steps {
                script {
                    sh 'docker build -t vimal2601/multi:v3 .'
                    sh 'docker images'
                }
            }
        }
        stage('Docker Push') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
                        sh "docker login -u vimalshero -p ${dockerPassword}"
                        sh 'docker tag  vimal2601/multi:v3'
                        sh 'docker push vimal2601/multi:v3'
                        sh 'docker rmi vimal2601/multi:v3'
                    }
                }
            }
        }
        stage('Deploy on k8s') {
            steps {
                script {
                    withKubeCredentials(kubectlCredentials: [[ credentialsId: 'kubernetes', namespace: 'ms' ]]) {
                        sh 'kubectl apply -f kube.yaml'
                        sh 'kubectl get pods -o wide'
                    }
                }
            }
        }
    }
}
