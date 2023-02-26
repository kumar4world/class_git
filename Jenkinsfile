pipeline {
    agent any
    
    environment {
        PROJECT_ID = 'pf-production'
        CLUSTER_NAME = 'peppyfamily'
        ZONE = 'us-central1-a'
        IMAGE_NAME = 'hello-app'
        IMAGE_TAG = 'mytag'
        KUBECONFIG = credentials('pf-production-249619-813c92c7280d.json')
    }
    
    stages {
        stage('Build') {
            steps {
                sh "git clone https://github.com/GoogleCloudPlatform/kubernetes-engine-samples
cd kubernetes-engine-samples/hello-app"
                sh "cd kubernetes-engine-samples/hello-app"
                sh "docker build -t us-central1-a-docker.pkg.dev/${PROJECT_ID}/hello-repo/hello-app:v1 ."
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
                sh "docker tag us-central1-a-docker.pkg.dev/${PROJECT_ID}/hello-repo/hello-app:v1 gcr.io/${PROJECT_ID}/${IMAGE_NAME}:${IMAGE_TAG}"
                sh "docker push gcr.io/${PROJECT_ID}/${IMAGE_NAME}:${IMAGE_TAG}"
            }
        }
        
        stage('Deploy') {
            steps {
                withKubeConfig([credentialsId: "${KUBECONFIG}"]) {
                    sh "gcloud container clusters get-credentials ${CLUSTER_NAME} --zone=${ZONE} --project=${PROJECT_ID}"
                    sh "kubectl create deployment hello-app --image=us-central1-a-docker.pkg.dev/${PROJECT_ID}/hello-repo/hello-app:v1"
                }
            }
        }
    }
}

