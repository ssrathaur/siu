#!/usr/bin/env groovy
node {
  stage('Execute shell') {
    var = "name"
    sh "echo Hello ${var}"
    build job: "/ssrathaur/test1/${env.BRANCH}", wait: true
 }
  
}
