pipeline {
  agent any
  options {
    skipDefaultCheckout false
  }

  environment {
    harbor=credentials('harbor')
    IMAGE_TAG = sh(returnStdout: true, script: "git rev-parse --short=10 HEAD").trim()
  }
  stages {
    stage("Build Docker Images") {
      parallel {
        stage("Build hello-nginx Image") {
          steps {
            dir('hello-nginx') {
              sh 'docker build -t 10.33.109.104/parallel-jobs/hello-nginx:${IMAGE_TAG} .'
            }
          }
        }
        stage("Build pallete Image") {
          steps {
            dir('pallete'){
              sh 'docker build -t 10.33.109.104/parallel-jobs/pallete:${IMAGE_TAG} .'
            }
          }
        }
        stage("Build pengedit-md Image") {
          steps {
            dir('pengedit-md'){
              sh 'docker build -t 10.33.109.104/parallel-jobs/pengedit-md:${IMAGE_TAG} .'
            }
          }
        }
        stage("Build hello-py Image") {
          steps {
            dir('hello-py'){
              sh 'docker build -t 10.33.109.104/parallel-jobs/hello-py:${IMAGE_TAG} .'
            }
          }
        }
      }
    }

    stage("Remove Orpans Containers") {
      steps {
        sh 'docker-compose -p parallel-jobs down'
      }
    }

    stage("Login to Harbor Registry") {
      steps {
        sh 'echo $harbor_PSW | docker login 10.33.109.104 -u $harbor_USR --password-stdin'
      }
    }
    stage("Push New Images") {
      parallel {
        stage("Push hello-nginx Image") {
          steps {
            sh 'docker push 10.33.109.104/parallel-jobs/hello-nginx:${IMAGE_TAG}'
          }
        }
        stage("Push pallete Image") {
          steps {
            sh 'docker push 10.33.109.104/parallel-jobs/pallete:${IMAGE_TAG}'
          }
        }
        stage("Push pengedit-md Image") {
          steps {
            sh 'docker push 10.33.109.104/parallel-jobs/pengedit-md:${IMAGE_TAG}'
          }
        }
        stage("Push hello-py Image") {
          steps {
            sh 'docker push 10.33.109.104/parallel-jobs/hello-py:${IMAGE_TAG}'
          }
        }
      }
    }

    stage("Run Containers") {
      steps {
        sh 'sed -i "s/latest/${IMAGE_TAG}/g" docker-compose.yaml'
        sh 'docker-compose -p parallel-jobs up -d'
      }
    }

    stage("Deploy in Kubernetes Production"){
      steps {
        dir('k8s-files'){
          sh 'kubectl set image deployment/hello-nginx hello-nginx=10.33.109.104/parallel-jobs/hello-nginx:${IMAGE_TAG} -n parallel-jobs'
          sh 'kubectl set image deployment/hello-py hello-py=10.33.109.104/parallel-jobs/hello-py:${IMAGE_TAG} -n parallel-jobs'
          sh 'kubectl set image deployment/pallete pallete=10.33.109.104/parallel-jobs/pallete:${IMAGE_TAG} -n parallel-jobs'
          sh 'kubectl set image deployment/pengedit-md pengedit-md=10.33.109.104/parallel-jobs/pengedit-md:${IMAGE_TAG} -n parallel-jobs'
        }
      }
    }
  }

  post {
    success {
      sh 'docker images'
      sh 'kubectl get pods -n parallel-jobs'
    }
  }
}