pipeline {
    agent any

    environment {
        IMAGE_NAME = "localhost:5000/cpp-demo"
        VERSION = "1.0.${BUILD_NUMBER}"
        IMAGE_TAG = "${IMAGE_NAME}:${VERSION}"
    }

    stages {

        stage('Checkout') {
            steps {
                sh '''
                rm -rf service
                git clone https://github.com/wyidongh/cpp-demo-service.git service
                '''
            }
        }

        stage('Build') {
            steps {
                sh '''
                cat > Dockerfile.build << 'EOF'
FROM cpp-ci:build-2.0
COPY service/ /workspace/
WORKDIR /workspace
RUN cmake -S . -B /build && cmake --build /build -j
EOF

                docker build -f Dockerfile.build -t ${IMAGE_TAG} .

                docker push ${IMAGE_TAG}
                '''
            }
        }

        stage('Output') {
            steps {
                sh '''
                echo ${IMAGE_TAG} > image.txt
                '''
                archiveArtifacts artifacts: 'image.txt'
            }
        }
    }
}
