pipeline {
    agent any

    tools {
        // 这里的 'maven' 和 'jdk21' 必须与你在 Jenkins 全局工具配置中的名称一致
        maven 'maven3' 
        jdk 'jdk21' 
    }

    stages {
        stage('Checkout') {
            steps {
                // 从 SCM (Git) 检出代码
                checkout scm
            }
        }

        stage('Build & PMD Check') {
            steps {
                // 执行 Maven 编译，跳过测试（测试在下一阶段单独运行），并运行 PMD 代码检查
                sh 'mvn clean compile pmd:pmd pmd:cpd -DskipTests'
            }
        }

        stage('Unit Tests') {
            steps {
                // 运行单元测试
                sh 'mvn test'
            }
            post {
                always {
                    // 收集并展示测试报告
                    junit '**/target/surefire-reports/TEST-*.xml'
                }
            }
        }

        stage('Generate JavaDoc') {
            steps {
                // 生成 JavaDoc 文档
                sh 'mvn javadoc:javadoc'
            }
        }

        stage('Package') {
            steps {
                // 打包生成可执行文件 (Jar/War)
                sh 'mvn package -DskipTests'
            }
        }
    }

    post {
        always {
            // 归档构建产物 (Jar/War)
            archiveArtifacts artifacts: '**/target/*.jar, **/target/*.war', fingerprint: true
            
            // 归档 PMD 报告 (需要安装 PMD 插件)
            pmd canComputeNew: false, defaultEncoding: '', healthy: '', pattern: '**/target/pmd.xml', unHealthy: ''
            
            // 归档 JavaDoc
            archiveArtifacts artifacts: '**/target/site/apidocs/**', allowEmptyArchive: true
        }
    }
}