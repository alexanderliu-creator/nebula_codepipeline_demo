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
                    echo "==================================================== Before checking scm"
                    checkout scm: [$class: 'GitSCM', branches: [[name: "*/${branchName}"]], userRemoteConfigs: [[url: 'https://github.com/alexanderliu-creator/nebula_codepipeline_demo/']]]
                    echo "==================================================== After checking scm"
                    // 打印当前分支名
                    echo "Branch name: ${branchName}"
                }
            }
        }

        stage('Build') {
            steps {
                script{
                    app = docker.build("tamar-jenkins-test")
                    echo "Docker image built: tamar-jenkins-test"
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

                    // 使用新的镜像标签进行推送
                    docker.withRegistry('https://186296540553.dkr.ecr.us-west-2.amazonaws.com/tamar-jenkins-test', 'ecr:us-west-2:181266c6-4c43-4088-bd78-cf889a1643e7') {
                        app.push(newTag)
                    }

                    echo "ECR image URI is: 186296540553.dkr.ecr.us-west-2.amazonaws.com/tamar-jenkins-test:${newTag}"
                }
            }
        }
    }
}
