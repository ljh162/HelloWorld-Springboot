pipeline {
    agent any

    environment {
        REGISTRY = "registry.cn-hangzhou.aliyuncs.com/your-namespace"  // 你的私库地址
        IMAGE_NAME = "springboot-demo"
        IMAGE_TAG = "latest"
        DOCKER_CREDENTIALS_ID = "docker-login"  // Jenkins中配置的凭据ID
    }

    stages {
        stage('检出代码') {
            steps {
                git branch: 'main', url: 'https://github.com/ljh162/HelloWorld-Springboot.git'
            }
        }

        stage('构建 Jar 包') {
            steps {
                echo "开始编译项目..."
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('构建 Docker 镜像') {
            steps {
                echo "正在构建 Docker 镜像..."
                sh '''
                    docker build -t ${REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG} .
                '''
            }
        }

        stage('推送镜像到私库') {
            steps {
                echo "登录并推送到镜像仓库..."
                withCredentials([usernamePassword(credentialsId: "${DOCKER_CREDENTIALS_ID}", passwordVariable: 'DOCKER_PASS', usernameVariable: 'DOCKER_USER')]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login ${REGISTRY} -u "$DOCKER_USER" --password-stdin
                        docker push ${REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG}
                        docker logout ${REGISTRY}
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "部署成功，镜像已推送到 ${REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG}"
        }
        failure {
            echo "构建失败，快去 Jenkins 控制台查日志。"
        }
    }
}
