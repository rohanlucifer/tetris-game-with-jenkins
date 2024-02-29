pipeline{
    agent any
    stages {
        stage('check the current directory'){
            steps{
                sh 'pwd'
            }
        }
        stage('tetris game folder'){
            steps{
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'secretFile')]){
                sh """
                pwd
                cd tetris-game
                export KUBECONFIG=$secretFile
                kubectl get nodes
                """
                }
            }
        }
        stage('apply the manifest file'){
            steps{
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'secretFile')]){
                sh """
                cd tetris-game
                pwd
                export KUBECONFIG=$secretFile
                kubectl apply -f deploy.yaml
                kubectl wait --for=condition=Ready --timeout=60s -l app=tetris pod
                """
                }
            }
        }
        stage('list out the deployment and services'){
            steps{
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'secretFile')]){
                sh """
                cd tetris-game
                export KUBECONFIG=$secretFile
                kubectl get all
                """
                }
            }
        }
         stage('provide metallb for external LB'){
            steps{
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'secretFile')]){
                sh """
                cd tetris-game
                pwd
                export KUBECONFIG=$secretFile
                kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.13.5/config/manifests/metallb-native.yaml
                kubectl wait --for=condition=Ready --timeout=60s -l app=metallb pod -n metallb-system
                """
                }
            }
        }
        stage('metallb for external LB'){
            steps{
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'secretFile')]){
                sh """
                cd tetris-game
                pwd
                export KUBECONFIG=$secretFile
                kubectl apply -f metallb.yaml
                kubectl get all
                """
                }
            }
        }
        stage('apply ingress'){
            steps{
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'secretFile')]){
                sh """
                cd tetris-game
                pwd
                export KUBECONFIG=$secretFile
                kubectl apply -f ingress.yaml
                kubectl get ing
                """
                }
            }
        }
    
    }
}
