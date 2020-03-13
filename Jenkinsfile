#!/usr/bin/env groovy
node {
  stage('Execute shell') {
    var = "name"
    sh "echo Hello ${var}"
 }
  stage('Trigger Branch Build') {
        steps {
            script {
                    echo "Triggering job for branch ${env.BRANCH_NAME}"
                    BRANCH_TO_TAG=env.BRANCH_NAME.replace("/","%2F")
                    build job: "../siu/${siu}", wait: false
            }
        }
    }
}
