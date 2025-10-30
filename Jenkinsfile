pipeline {
    agent any

    environment {
        // Update these with your details
        GIT_REPO = 'https://github.com/your-org/your-app.git'
        SONARQUBE_SERVER = 'SonarQubeServer'   // name configured in Jenkins
        SONARQUBE_PROJECT_KEY = 'my-app'
        DOCKER_IMAGE = 'your-dockerhub-username/your-app'
        NEXUS_REPO = 'nexus-releases'
        NEXUS_CREDENTIALS = 'nexus-creds'
        DOCKER_CREDENTIALS = 'dockerhub-creds'
        KUBE_CONFIG = credentials('eks-kubeconfig')  // stored in Jenkins
    }

    stages {
        stage('Checkout Code') {
            steps {
                echo "Checking out code from Git..."
                git branch: 'main', url: "${GIT_REPO}"
            }
        }

        stage('SonarQube Code Analysis') {
            steps {
                echo "Running SonarQube analysis..."
                withSonarQubeEnv("${SONARQUBE_SERVER}") {
                    sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=${SONARQUBE_PROJECT_KEY}'
                }
            }
        }

        stage('Build with Maven') {
            steps {
                echo "Building the project with Maven..."
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Upload Artifact to Nexus') {
            steps {
                echo "Uploading artifact to Nexus..."
                withCredentials([usernamePassword(credentialsId: "${NEXUS_CREDENTIALS}", usernameVariable: 'NEXUS_USER', passwordVariable: 'NEXUS_PASS')]) {
                    sh '''
                        mvn deploy:deploy-file \
                        -DgroupId=com.example \
                        -DartifactId=my-app \
                        -Dversion=1.0.0 \
                        -Dpackaging=jar \
                        -Dfile=target/my-app.jar \
                        -DrepositoryId=nexus \
                        -Durl=http://<NEXUS_URL>/repository/${NEXUS_REPO}/ \
                        -Dusername=$NEXUS_USER -Dpassword=$NEXUS_PASS
                    '''
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker image..."
                sh 'docker build -t ${DOCKER_IMAGE}:latest .'
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                echo "Pushing Docker image to Docker Hub..."
                withCredentials([usernamePassword(credentialsId: "${DOCKER_CREDENTIALS}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                        docker push ${DOCKER_IMAGE}:latest
                    '''
                }
            }
        }

        stage('Deploy to EKS') {
            steps {
                echo "Deploying to Amazon EKS..."
                withCredentials([file(credentialsId: 'eks-kubeconfig', variable: 'KUBECONFIG')]) {
                    sh '''
                        kubectl apply -f k8s/deployment.yaml
                        kubectl apply -f k8s/service.yaml
                        kubectl rollout status deployment/my-app-deployment
                    '''
                }
            }
        }
    }

    post {
        always {
            echo "Cleaning up workspace..."
            cleanWs()
        }
        success {
            echo "✅ Application deployed successfully to EKS!"
        }
        failure {
            echo "❌ Build failed! Check logs for details."
        }
    }
}
