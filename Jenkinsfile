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
                    echo "==================================================== Before checking"
                    checkout scm: [$class: 'GitSCM', branches: [[name: "*/${branchName}"]], userRemoteConfigs: [[url: 'https://github.com/alexanderliu-creator/nebula_codepipeline_demo/']]]

                    def currentBranch = scm.branches[0].name
                    if (currentBranch.contains ("*/")){
                        currentBranch = currentBranch.split("\\*/")[1]
                    }
                    echo "==================================================== Now ${currentBranch}"
                    echo "==================================================== After checking"
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
                    // 检查分支名是否为空
                    if (env.BRANCH_NAME == null) {
                        echo "BRANCH_NAME environment variable is not set. Defaulting to 'unknown-branch'."
                        env.BRANCH_NAME = 'unknown-branch'
                    }

                    // def branch_nem = scm.branches[0].name
                    // if (branch_nem.contains("*/")){
                    //     branch_nem = branch_nem.split("\\*/")[1]
                    // }
                    // echo branch_nem

                    def branchName = params.BranchName.split('/').last()
                    def buildTime = new Date().format("yyyyMMddHHmm", TimeZone.getTimeZone('UTC'))
                    def newTag = "${branchName}-${buildTime}"
                    def repoURI = echo "https://186296540553.dkr.ecr.us-west-2.amazonaws.com/${params.ECRRepoName}"

                    // 使用新的镜像标签进行推送
                    docker.withRegistry(repoURI, 'ecr:us-west-2:181266c6-4c43-4088-bd78-cf889a1643e7') {
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