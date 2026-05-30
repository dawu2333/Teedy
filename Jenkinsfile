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

        // 将 Build 和 PMD Analysis 合并
        stage('Build & PMD') {
            steps {
                // 在打包的同时执行 PMD 检查
                bat 'mvn clean package pmd:pmd -DskipTests -U'
            }
            post {
                always {
                    // 记录 PMD 警告报告
                    recordIssues tools: [pmdParser(pattern: '**/target/pmd.xml')]
                }
            }
        }

        stage('Test') {
            steps {
                // 运行单元测试
                bat 'mvn test -U'  // 加上 -U
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
                // bat 'mvn javadoc:javadoc -U'  // 加上 -U
                bat 'mvn javadoc:javadoc -Ddoclint=none -U'
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
