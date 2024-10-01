pipeline {
  agent  any
    stages {

      stage ('Checkout SCM'){
        steps {
          checkout([$class: 'Github', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'git', url: 'https://github.com/ramadevikodi/terraform.git']]])
        }
      }

     
      stage('Set Terraform path') {
       steps {
         script {
            def tfHome = tool name: 'terraform'
            env.PATH = "${tfHome}:${env.PATH}"
         }
     }
  }
  stage('terraform init') {
 
       steps {
           dir ("terraform") {
                script {
                    withAWS(roleAccount:'127214170915', role:'jenkins-role', useNode: true) {
                    sh 'terraform $action'
                    }
             }
           }
        }
      }

  stage('terraform Plan') {
 
       steps {
           dir ("terraform") {
            
               script {
                    withAWS(roleAccount:'127214170915', role:'jenkins-role', useNode: true) {
                    sh 'terraform $action -out=plan.out'
                    }
               }
            }
        }
      }

  stage('Waiting for Approvals') {
            
      steps{
          input('Plan Validated? Please approve to create VPC Network in AWS?')
			  }
      }    

  stage('terraform Apply') {
 
       steps {
           dir ("terraform") {
            
              script {
                    withAWS(roleAccount:'127214170915', role:'jenkins-role', useNode: true) {
                    sh 'terraform $action -auto-approve plan.out'
                    sh "terraform output"
                    }
              }
            
           }
       }
  }
	    
stage('terraform destroy') {
 
       steps {
           dir ("terraform") {
            
              script {
                    withAWS(roleAccount:'127214170915', role:'jenkins-role', useNode: true) {
                    sh 'terraform $action -auto-approve'
                
                    }
              }
            
           }
        }
      }
   }
}
