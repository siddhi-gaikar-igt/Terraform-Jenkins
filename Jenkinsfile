pipeline {

    parameters {
        booleanParam(name: 'autoApprove', defaultvalue: false, description: 'Automatically run apply after generating plan?')
    }
    environment {
        AWS_ACCESS_KEY_ID     = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
   }
  agent any
   stages {
       stage('checkout') {
           steps {
                script{
                       dir("terraform")
                       {
                          git "https://github.com/siddhi-gaikar-igt/Terraform-Jenkins.git"
                      }
                  }
              }
           }
        stage('Plan') {
            steps {
                steps {
                     sh 'pwd;cd terraform/ ; terraform init'
                     sh "pwd;cd terraform/ ; terraform plan -out tfplan"
                     sh 'pwd;cd terraform/ ; terraform show -no-colour tfplan > tfplan.txt'
                }
       }
       stage('Approval') {
          when {
              not {
                  equals expected:true, actual: params.autoApprove
              }
          }
       steps {
            script {
                 def plan = read file 'terraform/plan.txt'
                 input message: "Do you want to apply the plan?",
                 parameters: [text(name: 'plan', description:'please review the plan', default value: plan)]
            }
       }
   }

       stage('Apply') {
           steps {
               sh "pwd;cd terraform/ ; terraform apply -input-false tfplan"
            }
       }

  }

}

}


