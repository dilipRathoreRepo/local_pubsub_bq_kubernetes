pipeline {

  environment {
    PROJECT = "rising-minutia-254502"
    APP_NAME = "pubsub_bq_kubernetes"
    CLUSTER = "jenkins-cd"
    CLUSTER_ZONE = "us-east1-d"
    IMAGE_TAG = "gcr.io/${PROJECT}/${APP_NAME}:v\"${env.BUILD_NUMBER}\""
    JENKINS_CRED = "vivid-fragment-222314"
  }

  agent {
    kubernetes {
      label 'pubsub_bq_kubernetes-app'
      defaultContainer 'jnlp'
      yaml """
apiVersion: v1
kind: Pod
metadata:
labels:
  component: ci
spec:
  # Use service account that can deploy to all namespaces
  serviceAccountName: cd-jenkins
  containers:
  - name: golang
    image: golang:1.10
    command:
    - cat
    tty: true
  - name: gcloud
    image: gcr.io/cloud-builders/gcloud
    command:
    - cat
    tty: true
  - name: kubectl
    image: gcr.io/cloud-builders/kubectl
    command:
    - cat
    tty: true
"""
}
  }
  stages {
//    stage('Test') {
//      steps {
//        container('python') {
//          sh """
//            ln -s `pwd` /go/src/sample-app
//            cd /go/src/sample-app
//            sh "python -m pytest" 
//          """
//        }
//      }
//    }
//    stage('Build and push image with Container Builder') {
//      steps {
//        container('gcloud') {
//          sh "PYTHONUNBUFFERED=1 gcloud builds submit -t ${IMAGE_TAG} ."
//        }
//      }
//    }
    stage('Deploy Production') {
      // Production branch
      // when { branch 'master' }
      steps{
        container('kubectl') {
        // Change deployed image in production to the one we just built
          //  sh("sed -i.bak 's#gcr.io/cloud-solutions-images/gceme:1.0.0#${IMAGE_TAG}#' ./k8s/production/*.yaml")
          step([$class: 'KubernetesEngineBuilder',namespace:'production', projectId: env.PROJECT, clusterName: env.CLUSTER, zone: env.CLUSTER_ZONE, manifestPattern: 'twitter-stream.yaml', credentialsId: env.JENKINS_CRED, verifyDeployments: true])
          step([$class: 'KubernetesEngineBuilder',namespace:'production', projectId: env.PROJECT, clusterName: env.CLUSTER, zone: env.CLUSTER_ZONE, manifestPattern: 'bigquery-controller.yaml', credentialsId: env.JENKINS_CRED, verifyDeployments: true])
        }
      }
    }
  }
}
