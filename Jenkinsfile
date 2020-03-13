#!/usr/bin/env groovy
node {
  stages{
  stage('Execute shell') {
    var = "name"
    sh "echo Hello ${var}"
 }
  stage('execute any way')
  {
    steps {
        script {
             def userInput = input(id: 'userInput', message: 'Merge to?',
             parameters: [[$class: 'ChoiceParameterDefinition', defaultValue: 'strDef', 
                description:'describing choices', name:'nameChoice', choices: "QA\nUAT\nProduction\nDevelop\nMaster"]
             ])
        }
            build job: "${userInput}" //Use this value to branch to different logic if needed
        }
    }
 }
  
  
}
