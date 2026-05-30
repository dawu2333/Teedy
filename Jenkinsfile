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

        // 将 Build、PMD 和 JavaDoc 全部合并到一个 Maven 反应堆中
        stage('Build & PMD & JavaDoc') {
            steps {
                // 一条命令搞定打包、代码检查、文档生成
                bat 'mvn clean package pmd:pmd javadoc:javadoc -DskipTests -Ddoclint=none -U'
            }
            post {
                always {
                    // 记录 PMD 警告报告
                    recordIssues tools: [pmdParser(pattern: '**/target/pmd.xml')]
                    // 归档生成的 JavaDoc 文档 (HTML格式)
                    archiveArtifacts artifacts: '**/target/site/apidocs/**', allowEmptyArchive: true
                }
            }
        }

        stage('Test') {
            steps {
                // 运行单元测试
                bat 'mvn test -U'
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
