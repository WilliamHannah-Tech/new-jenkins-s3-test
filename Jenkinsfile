pipeline {
    agent any
    environment {
        AWS_REGION = 'us-east-1' 
    }
    stages {
        stage('Set AWS Credentials') {
            steps {
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'jenkinstest01' 
                ]]) {
                    sh '''
                    echo "AWS_ACCESS_KEY_ID: $AWS_ACCESS_KEY_ID"
                    aws sts get-caller-identity
                    '''
                }
            }
        }
    stage {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/WilliamHannah-Tech/new-jenkins-s3-test.git'
            }
        }

        stage('Initialize Terraform') {
            steps {
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'jenkinstest01'
                ]]) {
                    sh  '''
                    export AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID
                    export AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY
                    terraform init 
                    '''
                }
            }
        }

        stage('Plan Terraform') {
            steps {
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'jenkinstest01'
                ]]) {
                    sh  '''
                    export AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID
                    export AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY
                    terraform plan -out=tfplan
                    '''
                }
            }
        }

        stage('Apply Terraform') {
            steps {
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'jenkinstest01'
                ]]) {
                    sh  '''
                    export AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID
                    export AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY
                    terraform apply -auto-approve tfplan
                    '''
                }
            }
        }
    }
    //     stage('Optional Destroy') {
    //         steps {
    //             script {
    //                 def destroyChoice = input(
    //                     message: 'Do you want to run terraform destroy?',
    //                     ok: 'Submit',
    //                     parameters: [
    //                         choice(
    //                             name: 'DESTROY',
    //                             choices: ['no', 'yes'],
    //                             description: 'Select yes to destroy resources'
    //                         )
    //                     ]
    //                 )

    //                 if (destroyChoice == 'yes') {
    //                     withCredentials([[
    //                         $class: 'AmazonWebServicesCredentialsBinding',
    //                         credentialsId: 'jenkinstest01'
    //                     ]]) {
    //                         sh 'terraform destroy -auto-approve'
    //                     }
    //                 } else {
    //                     echo "Skipping destroy"
    //                 }
    //             }
    //         }
    //     }
    // }

    post {
        success {
            echo 'Terraform deployment comopleted successfully!'
        }
        failure {
            echo 'Terraform deployment failed!'
        }
    }
}
}
