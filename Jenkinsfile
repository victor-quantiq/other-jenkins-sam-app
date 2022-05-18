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
        sh 'python3.8 -m pytest tests/unit/test_handler.py'
        stash includes: '**/.aws-sam/**/*', name: 'aws-sam'
      }
    }
    stage('Deploy') {
      steps {
        withAWS(credentials: 'aws-admin-credentials', region: 'eu-west-3') {
          unstash 'aws-sam'
          sh 'sam deploy'

          script {
              try {
                sh 'python3.8 -m unittest tests/integration/test_api_gateway.py'
              }
              catch (Exception e) {
              if integration failed, no simple way to rolll-back the sam deployment. So, we go to the previous commit (stable version), and redeploy. Then we break the pipeline
              sh 'git reset --hard HEAD~1 && sam build --config-env dev && sam deploy --config-env dev && wget --post-data="" $BUILD_URL/stop'
              }
          }
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
