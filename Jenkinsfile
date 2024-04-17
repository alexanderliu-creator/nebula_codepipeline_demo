pipeline {
    agent any
    options {
        skipStagesAfterUnstable()
    }
    stages {
         stage('Clone repository') {
            steps {
                script{
                    def branchName = params.BranchName.split('/').last()
                    // 使用简化的checkout命令动态指定分支名
                    echo "==================================================== Before checking sum"
                    checkout scm: [$class: 'GitSCM', branches: [[name: "*/${branchName}"]], userRemoteConfigs: [[url: 'https://github.com/alexanderliu-creator/nebula_codepipeline_demo/']]]
                    echo "==================================================== After checking sum"
                    // 打印当前分支名
                    echo "Branch name: ${branchName}"
                }
            }
        }

        stage('Build') {
            steps {
                script{
                    app = docker.build(params.ECRRepoName)
                    echo "Docker image built: ${params.ECRRepoName}"
                }
            }
        }
        stage('Test'){
            steps {
                 echo 'Empty'
            }
        }
        stage('Deploy') {
            steps {
                script{
                    def branchName = params.BranchName.split('/').last()
                    def buildTime = new Date().format("yyyyMMddHHmm", TimeZone.getTimeZone('UTC'))
                    def newTag = "${branchName}-${buildTime}"
                    def ECRURI = echo "https://186296540553.dkr.ecr.us-west-2.amazonaws.com/${params.ECRRepoName}"

                    // 使用新的镜像标签进行推送
                    docker.withRegistry(${ECRURI}, 'ecr:us-west-2:181266c6-4c43-4088-bd78-cf889a1643e7') {
                        app.push(newTag)
                        echo "Images pushed: ${newTag} and latest"
                        // app.push("latest")
                        // app.push()
                    }
                }
            }
        }
    }
}