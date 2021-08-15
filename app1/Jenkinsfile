pipeline {
    agent any
       triggers {
        pollSCM "* * * * *"
       }
    stages {
        stage('Build Docker Image') {
          //  when {
            //    branch 'master'
          //  }
            steps {
                echo '=== Building Docker Image ==='
                // sh "docker-compose build"
                // sh "docker tag react-dev 412857254796.dkr.ecr.us-east-1.amazonaws.com/react-dev:latest"
                // sh "docker tag node-dev 412857254796.dkr.ecr.us-east-1.amazonaws.com/node-dev:latest"
		dir("app1") {
			docker build -t my-nginx . 
			docker tag my-nginx:latest 412857254796.dkr.ecr.us-east-1.amazonaws.com/my-nginx:latest
			}
            }
        }
        stage('Push Docker Image') {
           // when {
           //     branch 'master'
           // }
            steps {
                echo '=== Pushing Docker Image ==='
                script {
                    GIT_COMMIT_HASH = sh (script: "git log -n 1 --pretty=format:'%H'", returnStdout: true)
                    SHORT_COMMIT = "${GIT_COMMIT_HASH[0..7]}"
                    docker.withRegistry('', 'dockerHubCredentials') {
                        sh "aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 412857254796.dkr.ecr.us-east-1.amazonaws.com"
                        sh "docker push 412857254796.dkr.ecr.us-east-1.amazonaws.com/my-nginx:latest"
                    }
                }
            }
        }
        stage('Remove local images') {
            steps {
                echo '=== Delete the local docker images ==='
                 sh("docker rmi -f my-nginx:latest 412857254796.dkr.ecr.us-east-1.amazonaws.com/my-nginx:latest || :")
            }
        }
        stage('Deploy to EKS') {
            steps {
                echo '=== Deploy to EKS Cluster ==='
                //sh("ansible-playbook playbook.yaml")
            }
        }
    }
}

