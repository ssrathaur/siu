#!/usr/bin/env groovy
node {
  stage('Execute shell') {
    var = "name"
    sh "echo Hello ${var}"
    if (env.BRANCH_NAME == 'master') {
    build '../test1/master'
}
 }
  
}
