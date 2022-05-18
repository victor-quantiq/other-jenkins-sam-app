pipeline {
  agent any

  stages {
    // stage('Install sam-cli') {
    //   steps {
    //     sh 'python3 -m venv venv && venv/bin/pip install aws-sam-cli'
    //     stash includes: '**/venv/**/*', name: 'venv'
    //   }
    // }
    stage('Build') {
      steps {
        // unstash 'venv'
        // sh 'venv/bin/sam build'
        sh 'sam build'
        // sh 'cd todos && npm install && npm run test && cd ../'
        stash includes: '**/.aws-sam/**/*', name: 'aws-sam'
      }
    }
    stage('Deploy') {
      steps {
        withAWS(credentials: 'aws-admin-credentials', region: 'eu-west-3') {
          unstash 'aws-sam'
          sh 'sam deploy'

          // script {
          //     try {
          //     dir ('todos') {
          //       sh 'npm ci'
          //       sh 'AWS_REGION=eu-west-3 STACK_NAME=todo-sam-app-dev npm test tests/integ/test-integ-api.js'
          //     }
          //     }
          //     catch (Exception e) {
          //     //if integration failed, no simple way to rolll-back the sam deployment. So, we go to the previous commit (stable version), and redeploy. Then we break the pipeline
          //     sh 'git reset --hard HEAD~1 && sam build --config-env dev && sam deploy --config-env dev && wget --post-data="" $BUILD_URL/stop'
          //     }
          // }
        }
      }
    }
    // stage('Promotion') {
    //   steps {
    //     sh 'git remote -v'
    //     sh 'git branch'
    //     sh 'git push origin HEAD:UAT'
    //   }
    // }
  }
}
