pipeline {
  agent any

  parameters {
    string(name: 'ENV', defaultValue: 'dev', description: 'Environment to deploy')
  }

  environment {
    AWS_ACCESS_KEY_ID     = credentials('aws-access-key')
    AWS_SECRET_ACCESS_KEY = credentials('aws-access-key')
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
        script {
          echo "Running for environment: ${params.ENV}"
        }
      }
    }

    stage('Validate Environment Folder') {
      steps {
        script {
          if (!fileExists("terraform/${params.ENV}/terraform.tfvars")) {
            error("No folder found for environment: terraform/${params.ENV}/terraform.tfvars")
          }
        }
      }
    }

    stage('Terraform Init') {
      steps {
        dir("terraform/${params.ENV}") {
          sh 'terraform init'
        }
      }
    }

    stage('Terraform Plan') {
      steps {
        dir("terraform/${params.ENV}") {
          sh "terraform plan -var-file=terraform.tfvars"
        }
      }
    }

    stage('Terraform Apply') {
      when {
        expression { return ['dev', 'stage', 'prod'].contains(params.ENV) }
      }
      steps {
        dir("terraform/${params.ENV}") {
          sh "terraform apply -auto-approve -var-file=terraform.tfvars"
        }
      }
    }
  }
}


