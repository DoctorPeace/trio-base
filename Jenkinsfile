pipeline {
    agent any
    stages {
        stage('Init') {
            steps {
                script {
			        if (env.GIT_BRANCH == 'origin/main') {
                        sh '''
                        kubectl create namespace trio-prod || echo "namespace trio-prod already exists"
                        echo "main:Init successful"
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                        sh '''
                        kubectl create namespace trio-dev || echo "namespace trio-dev already exists"
                        echo "dev:Init successful"
                        '''
                    } else {
                        sh '''
                        echo "Init - Unrecognised branch"
                        '''
                    }
                }
            }            
        }
        stage('Build') {
            steps {
                script {
			        if (env.GIT_BRANCH == 'origin/main') {
                        sh '''
                        docker build -t drpeace/trio-deploy-flask:latest -t drpeace/trio-deploy-flask:prod-v${BUILD_NUMBER} .
                        docker build -t drpeace/trio-mysql:latest -t drpeace/trio-mysql:prod-v${BUILD_NUMBER} .
                        echo "main:Build successful"
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                        sh '''
                        docker build -t drpeace/trio-deploy-flask:latest -t drpeace/trio-deploy-flask:dev-v${BUILD_NUMBER} .
                        docker build -t drpeace/trio-mysql:latest -t drpeace/trio-mysql:dev-v${BUILD_NUMBER} .
                        echo "dev:Build successful"
                        '''
                    } else {
                        sh '''
                        echo "Build - Unrecognised branch"
                        '''
                    }
                }
            }
        }
        stage('Push') {
            steps {
                script {
			        if (env.GIT_BRANCH == 'origin/main') {
                        sh '''
                        docker push drpeace/trio-deploy-flask
                        docker push drpeace/trio-deploy-flask:prod-v${BUILD_NUMBER}
                        docker push drpeace/trio-mysql
                        docker push drpeace/trio-mysql:prod-v${BUILD_NUMBER}
                        echo "dev:Push successful"
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                        sh '''
                        docker push drpeace/trio-deploy-flask
                        docker push drpeace/trio-deploy-flask:dev-v${BUILD_NUMBER}
                        docker push drpeace/trio-mysql
                        docker push drpeace/trio-mysql:dev-v${BUILD_NUMBER}
                        echo "dev:Push successful"
                        '''
                    } else {
                        sh '''
                        echo "Push - Unrecognised branch"
                        '''
                    }
                }    
           }
        }
        stage('Deploy') {
            steps {
                script {
                    if (env.GIT_BRANCH == 'origin/main') {
                        sh '''
                        kubectl apply -f ./kubernetes --namespace trio-prod
                        kubectl set image deployment/flask-deployment flask-container=drpeace/trio-deploy-flask:prod-v${BUILD_NUMBER} -n trio-prod
                        echo "main:Deploy successful"
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                        sh '''
                        kubectl apply -f ./kubernetes --namespace trio-dev
                        kubectl set image deployment/flask-deployment flask-container=drpeace/trio-deploy-flask:dev-v${BUILD_NUMBER} -n trio-dev
                        echo "dev:Deploy successful"
                        '''
                    } else {
                        echo "Deploy - Unrecognised branch"
                    }
                }
            }
        }
        stage('Cleanup') {
            steps {
                script {
                    if (env.GIT_BRANCH == 'origin/main') {
                        sh '''
                        docker system prune -f
                        echo "main:Cleanup successful"
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                        sh '''
                        docker system prune -f
                        echo "dev:Cleanup successful"
                        '''
                    } else {
                        echo "Cleanup - Unrecognised branch"
                    }
                } 
            }
        }
    }
}