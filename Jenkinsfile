pipeline {
  agent any

  stages {
    // stage('setup python env') {
    //   steps {
    //     sh 'pwd'
    //     sh 'source ../py-virtual-env/python3.9-venv/bin/activate'
    //     // stash includes: '**/venv/**/*', name: 'venv'
    //   }
    // }
    stage('Build') {
      steps {
        // unstash withPythonEnv('../py-virtual-env/python3.9-venv/bin/python') {'venv'
        // sh 'venv/bin/sam build'
        sh "sam build"
        sh "printenv"
        withPythonEnv('python3.9'){
        sh '''
        pip install pytest boto3
        python -m pytest tests/unit/test_handler.py
        '''
        }
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
                withPythonEnv('python3.9'){
                sh '''
                pip install boto3 requests
                python -m unittest tests/integration/test_api_gateway.py
                '''
                }
              }
              catch (Exception e) {
              //if integration failed, no simple way to rolll-back the sam deployment. So, we go to the previous commit (stable version), and redeploy. Then we break the pipeline
              sh 'git reset --hard HEAD~1 && sam build && sam deploy && wget --post-data="" $BUILD_URL/stop'
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
