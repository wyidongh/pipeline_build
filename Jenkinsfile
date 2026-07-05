pipeline {

    agent any

    environment {
        IMAGE_NAME = "localhost:5000/cpp-demo"
        VERSION = "1.0.${BUILD_NUMBER}"
        IMAGE_TAG = "${IMAGE_NAME}:${VERSION}"

        SERVICE_DIR = "${WORKSPACE}/service"
        BUILD_DIR   = "${WORKSPACE}/build"
    }

    stages {

        stage('Checkout') {
            steps {
                sh """
                    set -e
                    rm -rf service
                    git clone https://github.com/wyidongh/cpp-demo-service.git service
                """
            }
        }

        stage('Build Image') {
            steps {
                sh """
                    set -e

                    cat > Dockerfile.build << 'EOF'
FROM cpp-ci:build-2.0
COPY service/ /workspace/
WORKDIR /workspace
RUN cmake -S . -B /build && cmake --build /build -j
EOF

                    docker build -f Dockerfile.build -t ${IMAGE_TAG} .
                """
            }
        }

        stage('Push Image') {
            steps {
                sh """
                    set -e
                    docker tag ${IMAGE_TAG} ${IMAGE_TAG}
                    docker push ${IMAGE_TAG}
                """
            }
        }

        stage('Trigger Test Pipeline') {
            steps {
                script {
                    build job: 'pipeline_test',   // ⚠️ B job 名字
                        parameters: [
                            string(name: 'IMAGE', value: "${IMAGE_TAG}"),
                            string(name: 'VERSION', value: "${VERSION}")
                        ]
                }
            }
        }
    }

    post {
        success {
            echo "CI PIPELINE SUCCESS ✅"
        }

        failure {
            echo "CI PIPELINE FAILED ❌"
        }

        always {
            sh "rm -f Dockerfile.build || true"
        }
    }
}
