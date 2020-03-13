#!/usr/bin/env groovy
node {
  stage('Execute shell') {
    var = "name"
    sh "echo Hello ${var}"
 }
  stage('master') {
                    steps {
                            build job: "/ssrathaur/test1/${env.BRANCH}", wait: true
                    }
                }
}
