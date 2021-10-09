Pipeline {
    agent any
    environment {
        PATH=sh(script:"echo $PATH:/usr/local/bin", returnStdout:true).trim()
        AWS_REGION = "us-east-1"
        AWS_ACCOUNT_ID=sh(script:'export PATH="$PATH:/usr/local/bin" && aws sts get-caller-identity --query Account --output text', returnStdout:true).trim()
        ECR_REGISTRY="${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"
        APP_REPO_NAME = "clarusway-repo/phonebook-app"
        APP_NAME = "phonebook"
        AWS_STACK_NAME = "Call-Phonebook-App-${BUILD_NUMBER}"
        CFN_TEMPLATE="phonebook-docker-swarm-cfn-template.yml"
        CFN_KEYPAIR="ofd"
        HOME_FOLDER = "/home/ec2-user"
        GIT_FOLDER = sh(script:'echo ${GIT_URL} | sed "s/.*\\///;s/.git$//"', returnStdout:true).trim()
    }

    stages {

        stage('Create ECR Repo') {
            steps {
                echo 'Creating ECR Repo for App'
            }
        }

        stage('Build App Docker Image') {
            steps {
                echo 'Building App Image'
            }
        }

        stage('Push Image to ECR Repo') {
            steps {
                echo 'Pushing App Image to ECR Repo'
            }
        }

        stage('Create Infrastructure for the App') {
            steps {
                echo 'Creating Infrastructure for the App on AWS Cloud'
            }
        }

        stage('Test the Infrastructure') {
            steps {
                echo "Testing if the Docker Swarm is ready or not, by checking Viz App on Leader Master with Public Ip Address: ${MASTER_INSTANCE_PUBLIC_IP}:8080"
            }
        }

        stage('Deploy App on Docker Swarm'){
            steps {
                echo "Cloning and Deploying App on Swarm using Grand Master with Instance Id: $MASTER_INSTANCE_ID"
                    }
                }
            }
        }

    post {
        always {
            echo 'Deleting all local images'
            sh 'docker image prune -af'
        }
        failure {
            echo 'Delete the Image Repository on ECR due to the Failure'
            sh """
                aws ecr delete-repository \
                  --repository-name ${APP_REPO_NAME} \
                  --region ${AWS_REGION}\
                  --force
                """
            echo 'Deleting Cloudformation Stack due to the Failure'
                sh 'aws cloudformation delete-stack --region ${AWS_REGION} --stack-name ${AWS_STACK_NAME}'
        }
    }