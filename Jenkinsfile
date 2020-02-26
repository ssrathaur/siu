#!groovy
def jobName = 'upiswitchv2proxysql-service'
def devopsUsers= ['eka1cha25414','chandanmaheshwari','SanchitBansal','pranshu7','nishant1331','rishabhbajpai','ravimishrapaytm','vyom_srivastava_paytmbank','Rishabh1101']

node {
    deleteDir()
    def dockerImage = 'None'
    wrap([$class: 'BuildUser']) {



    stage("Authenticate User"){
      try{
          searchUser(devopsUsers,env.BUILD_USER_ID)
        }
        catch (exc) {
            echo "Authentication Failed"
            throw exc
        }
    }
    stage('Pre Build') {
        try {
            checkout scm
            echo "Cloning code from git branch ${env.BRANCH_NAME}"
        }
        catch (exc) {
            echo "unable to pull code from branch ${env.BRANCH_NAME}"
            throw exc
        }
    }
    stage('Package') {
        try {
            echo 'creating docker image'
            gitCommit = gitCommitId()
            sh "./package.sh ${gitCommit}"
            echo 'Docker Image created successfully'
        }
        catch (exc) {
            echo "Docker Image creation failed"
            throw exc
        }
    }

    stage('Push') {
        try {
            echo "Pushing the image to repo"
            gitCommit = gitCommitId()
            sh "./push.sh ${gitCommit}"
            echo "Docker image pushed successfully in the repo"
        }
        catch (exc) {
            echo "Unable to push image to repo"
            throw exc
        }
    }

    stage("checkout infrastructure-as-code for deployment "){


       def env = ['pt','stage','prod']
       def replica = ['2','1','2']


       gitCommit = gitCommitId()
       ws('/var/lib/jenkins/workspace/DeploymentPipelineUpiProxysql'){
       //Checkout function to checkout infrastructure as code for deployment
       checkout([$class: 'GitSCM', branches: [[name: '*/master']],userRemoteConfigs: [[url: 'git@bitbucket.org:paytmteam/infrastructure-as-code.git', credentialsId: 'bf57f39b-cfe0-4ac4-85e1-d8fff5d71a0b']]])
       for (i = 0; i <env.size(); i++) {
           def userInput = input(
                    id: 'userInputForDeployment', message: "Build is successful. Let\'s promote to ${env[i]}?", submitterParameter: 'submitter', parameters: [
                    [$class: 'BooleanParameterDefinition', defaultValue: false, description: "Deploy to ${env[i]}", name: env[i]],
                ])
           searchUser(devopsUsers,userInput['submitter'])

            if (userInput){

                 stage ("Deploy To ${env[i]}"){
                       try{
                          echo "Running deployment on ${env[i]}. Using docker image ${gitCommit}"

                          if (searchYaml(jobName)){
                            def file = readYaml file: "./kubernetes/${jobName}.yaml"
                            for (j=0;j<file.size();j++){

                                if (file[j].get('kind') == 'Deployment'){
                                    dockerImage = file[j].spec.template.spec.containers[0].image
                                }
                            }
                            dockerImage =dockerImage.split(':')

                            //shell scripts used for deployment
                            sh "mkdir -p ./kubernetes/deploy${env[i]}"
                            sh "touch ./kubernetes/deploy${env[i]}/${jobName}.yaml"
                            sh "cp ./kubernetes/${jobName}.yaml ./kubernetes/deploy${env[i]}/"
                            sh "sed -i 's/iteenv/${env[i]}/g' ./kubernetes/deploy${env[i]}/${jobName}.yaml"
                            sh "sed -i 's/${dockerImage[1]}/${gitCommit}/g' ./kubernetes/deploy${env[i]}/${jobName}.yaml"
                            sh "sed -i 's/replicas: 1/replicas: ${replica[i]}/g' ./kubernetes/deploy${env[i]}/${jobName}.yaml"
                            sh "sudo kubectl apply -f ./kubernetes/deploy${env[i]}/${jobName}.yaml --kubeconfig=/root/kubeconfig/kubeconfig-upi${env[i]}"
                           }
                          }
                        catch(exc){
                           echo "Unable to deploy on ${env[i]}"
                           throw exc
                          }
                        }
                      }
                    }
                  }
                }
      }

}

def gitCommitId() {
    gitCommit = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
    shortCommit = gitCommit.take(6)
    return shortCommit
}

def searchYaml(jobName){
  stdout = sh (script: 'ls ./kubernetes', returnStdout: true)
  jobNameSearch = jobName+'.yaml'
  JobNameExist = checkInList(stdout,jobNameSearch)
  if (JobNameExist){
    echo "using ${jobName}.yaml"
    return true
  }
  else {
    echo "${jobName}.yaml not found, Deployment failed"
    return false
  }
}

def checkInList(stdout,check){
  def b= stdout
  def value = b.contains(check)
  return value
}

def searchUser(devopsUsers,userName){
  users = devopsUsers
  userExist = checkInList(users,userName)
  if (userExist){
    echo "user authenticated"
  }
  else {
    error("User not allowed")
  }
}
