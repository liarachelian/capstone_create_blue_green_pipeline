pipeline{
    agent any
    stages{
        stage('Lint HTML') {
            steps {
                sh 'tidy -q -e app/*.php'
            }
        }
        stage('Build Docker Image') {
           steps {
                withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD']]){
                    sh '''
                        docker build -t adamsteff/capstonerepository:$BUILD_ID .
                    '''
                }

            }
        }
        stage('Push Docker Image') {
           steps {
                withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD']]){
                    sh '''
                        docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD
                        docker push adamsteff/capstonerepository:$BUILD_ID
                    '''
                }

            }
        }
        stage('Set Kubectl Context to Cluster') {
            steps{
                sh 'kubectl config use-context arn:aws:eks:ap-southeast-2:048353547478:cluster/capstonecluster'
            }
        }
        stage('Create Blue Controller') {
            when {
                expression { env.BRANCH_NAME == 'blue' }
            }
            steps{
                sh 'kubectl apply -f ./blue-controller.json'
            }
        }
        stage('Deploy to Production?') {
              when {
                expression { env.BRANCH_NAME == 'master' }
              }

              steps {
                // Prevent any older builds from deploying to production
                milestone(1)
                input 'Deploy to Production?'
                milestone(2)
              }
        }
    }
}