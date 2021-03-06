pipeline {
    agent any
    // 定数や変数を定義する
    environment {
        reportDir = 'build/reports'
        javaDir = 'src/main/java'
        resourcesDir = 'src/main/resources'
        testReportDir = 'build/test-results/test'
        jacocoReportDir = 'build/jacoco' 
        javadocDir = 'build/docs/javadoc'
        libsDir = 'build/libs'
        appName = 'SampleApp'
        appVersion = '1.0.0'
    }

    // stagesブロック中に一つ以上のstageを定義する
    stages {
        stage('事前準備') {
            // 実際の処理はstepsブロック中に定義する
            steps {
                deleteDir()

                // このJobをトリガーしてきたGithubのプロジェクトをチェックアウト
                checkout scm

                // ジョブ失敗の原因調査用にJenkinsfileとbuild.gradleは最初に保存する
                archiveArtifacts "Jenkinsfile"
                archiveArtifacts "build.gradle.kts"

                // scriptブロックを使うと従来のScripted Pipelinesの記法も使える
                script {
                    // Permission deniedで怒られないために実行権限を付与する
                    if(isUnix()) {
                        sh 'chmod +x gradlew'
                    }
                }
                gradlew 'clean'
            }
        }

        stage('コンパイル') {
            steps {
                gradlew 'classes testClasses'
            }
        }

        stage('テスト') {
            steps {
                gradlew 'test'

                junit "${testReportDir}/*.xml"
                archiveArtifacts "${testReportDir}/*.xml"
            }
        }
    }
}

// Gradlewコマンドを実行する
def gradlew(command) {
    if(isUnix()) {
        sh "./gradlew ${command} --stacktrace"
    } else {
        bat "./gradlew.bat ${command} --stacktrace"
    }
}
