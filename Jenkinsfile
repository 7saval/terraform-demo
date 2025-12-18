pipeline {
    agent any

    tools {
        terraform 'terraform-1.14.3'
    }

    stages {
        stage('Checkout') {
            steps {
                git(
                    branch: 'main',
                    url: 'https://github.com/7saval/terraform-demo.git',
                    credentialsId: 'github-credentials'
                )
            }
        }

        stage('Terraform init') {
            steps {
                withCredentials([
                    string(credentialsId: 'terraform-credentials',
                           variable: 'TERRAFORM_TOKEN')
                ]) {
                    sh '''
                        export TF_TOKEN_app_terraform_io="${TERRAFORM_TOKEN}"
                        terraform init
                    '''
                }
            }
        }

        stage('Terraform apply') {
            steps {
                withCredentials([
                    file(credentialsId: 'KUBECONFIG',
                         variable: 'KUBECONFIG_PATH'),
                    string(credentialsId: 'terraform-credentials',
                           variable: 'TERRAFORM_TOKEN')
                ]) {
                    sh '''
                        export TF_TOKEN_app_terraform_io="${TERRAFORM_TOKEN}"
                        terraform validate
                        terraform apply \
                          -var "kubernetes_config_path=${KUBECONFIG_PATH}" \
                          -auto-approve
                    '''
                }
            }
        }
    }
}
