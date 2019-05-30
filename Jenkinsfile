pipeline {
  agent any

  stages {
    stage('Build'){
      steps {
        echo 'Checking out ansible-playbooks repository...'
        dir('ansible'){
          script {
            if (env.BRANCH_NAME.startsWith('feature') || env.BRANCH_NAME.startsWith('develop')){
              def fallback_branch = "develop"
            } else {
              def fallback_branch = "master"
            }
            try {
              git branch: env.BRANCH_NAME, url: 'git@github.com:jinksunk/ansible-playbooks.git'
            } catch (Exception e){
              echo "Branch ${env.BRANCH_NAME} not found for ansible-playbooks, falling back to ${fallback_branch}..."
              git branch: fallback_branch, url: 'git@github.com:jinksunk/ansible-playbooks.git'
            }
          }
          echo 'Building ansible...'
          sh "chmod 755 ./build.sh && ./build.sh"

        }
      }
    }
    stage('Deploy') {
      steps {
        dir('ansible'){
          echo "Deploying with ansible playbook playbooks/${env.JOB_NAME.split('/')[0]}/install.yml..."
          ansiblePlaybook(inventory: 'inventory.yml', playbook: "playbooks/${env.JOB_NAME.split('/')[0]}/install.yml", extraVars: [workingdir: "${env.WORKSPACE}"], extras: "-v")
          echo " ... done."
        }
      }
    }
  }
}
