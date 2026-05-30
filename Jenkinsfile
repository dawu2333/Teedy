pipeline {
    agent any

    tools {
        // 确保这里的 'maven3' 和 'jdk21' 名称与你在 Jenkins 全局工具配置中的名称一致
        maven 'maven3'
        jdk 'jdk21'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                // 先跳过测试进行打包，加快构建速度
                bat 'mvn clean package -DskipTests'
            }
        }

        stage('PMD Analysis') {
            steps {
                // 执行 PMD 代码检查
                bat 'mvn pmd:pmd'
            }
            post {
                always {
                    // 记录 PMD 警告报告 (需要 Jenkins 安装 Warnings NG 插件)
                    recordIssues tools: [pmdParser(pattern: '**/target/pmd.xml')]
                }
            }
        }

        stage('Test') {
            steps {
                // 运行单元测试
                bat 'mvn test'
            }
            post {
                always {
                    // 归档 JUnit 测试结果 (XML格式)
                    junit '**/target/surefire-reports/*.xml'
                    // 归档测试报告 (HTML格式，如果有的话)
                    archiveArtifacts artifacts: '**/target/surefire-reports/*.html', allowEmptyArchive: true
                }
            }
        }

        stage('Generate JavaDoc') {
            steps {
                // 生成 JavaDoc 项目文档
                bat 'mvn javadoc:javadoc'
            }
            post {
                always {
                    // 归档生成的 JavaDoc 文档 (HTML格式)
                    archiveArtifacts artifacts: '**/target/site/apidocs/**', allowEmptyArchive: true
                }
            }
        }

        stage('Archive Artifacts') {
            steps {
                // 归档打包好的 JAR 可执行文件
                archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
            }
        }
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
