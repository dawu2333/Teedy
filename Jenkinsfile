pipeline {
    agent any

    tools {
        // 确保这里的 'maven' 和 'jdk' 名称与你在 Jenkins 全局工具配置中的名称一致
        maven 'maven3'
        jdk 'jdk21'
    }

    stages {
        stage('Checkout') {
            steps {
                // 从 GitHub 拉取代码
                checkout scm
            }
        }

        stage('Build') {
            steps {
                // Windows 下使用 bat 执行 Maven 打包命令
                bat 'mvn clean package -DskipTests'
            }
        }

        stage('Test') {
            steps {
                // Windows 下执行测试
                bat 'mvn test'
            }
            post {
                always {
                    // 归档测试报告，注意 Windows 路径使用双反斜杠
                    junit '**\\target\\surefire-reports\\*.xml'
                }
            }
        }

        stage('Archive') {
            steps {
                // 归档打包好的 JAR 文件
                archiveArtifacts artifacts: '**\\target\\*.jar', fingerprint: true
            }
        }

        // 暂时注释掉 PMD 静态代码分析，等安装好插件后再启用
        /*
        stage('PMD Analysis') {
            steps {
                bat 'mvn pmd:pmd'
            }
            post {
                always {
                    // 记录 PMD 警告
                    recordIssues tools: [pmdParser(pattern: '**\\target\\pmd.xml')]
                }
            }
        }
        */
    }

    post {
        always {
            // 构建结束后清理工作空间
            cleanWs()
        }
        success {
            echo '构建成功！'
        }
        failure {
            echo '构建失败！'
        }
    }
}
