pipeline {
  agent any

  environment {
    AWS_ACCESS_KEY_ID     = credentials('aws-access-key')
    AWS_SECRET_ACCESS_KEY = credentials('aws-access-key')
  }

  stages {
    stage('Checkout Code') {
      steps {
        checkout scm
        script {
          echo "Running from branch: ${env.BRANCH_NAME}"
        }
      }
    }

    stage('Validate Environment Folder') {
      steps {
        script {
          def envPath = "terraform/${env.BRANCH_NAME}/terraform.tfvars"
          if (!fileExists(envPath)) {
            error "No folder found for environment: ${envPath}"
          }
        }
      }
    }

    stage('Terraform Init') {
      steps {
        dir("terraform/${env.BRANCH_NAME}") {
          sh 'terraform init'
        }
      }
    }

    stage('Terraform Plan') {
      steps {
        dir("terraform/${env.BRANCH_NAME}") {
          sh 'terraform plan -var-file=terraform.tfvars'
        }
      }
    }

    stage('Terraform Apply') {
      when {
        branch pattern: "dev|stage|main", comparator: "REGEXP"
      }
      steps {
        dir("terraform/${env.BRANCH_NAME}") {
          sh 'terraform apply -auto-approve -var-file=terraform.tfvars'
        }
      }
    }
  }

  post {
    success {
      echo "✅ Terraform applied to ${env.BRANCH_NAME} successfully"
    }
    failure {
      echo "❌ Terraform apply failed"
    }
  }
}

