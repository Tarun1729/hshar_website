pipeline {
    agent any

     environment{
       registryCredential = 'ecr:<zone>:tarun1729'
       appRegistry = "277271872715.dkr.ecr.ap-northeast-1.amazonaws.com/capstoneproject"
       capstoneRegistry = "https://277271872715.dkr.ecr.ap-northeast-1.amazonaws.com"
       cluster = "capstone"
        service = "capstoneapp"
   }

    stages {
        stage('Clone Website') {
            steps {
                git url:'https://github.com/Tarun1729/hshar_website'
            }
        }

       stage("Docker Build Image"){
           steps{
             script{
                dockerImage = docker.build(appRegistry+ ":$BUILD_NUMBER",".")
             }
           }
      }

       stage('Test Website') {
            steps {
                // Run tests on the website
                sh 'echo "Running tests on the website"'
            }
       }

       stage("Upload App Image"){
         steps{
            script{
                docker.withRegistry(capstoneRegistry, registryCredential){
                    dockerImage.push("$BUILD_NUMBER")
                    dockerImage.push('latest')
                }
            }
         }
      }

      stage('Deploy to ecs'){
         when {
                branch "master"
         }
         steps{
            withAWS(credentials: 'tarun1729', region: 'ap-northeast-1'){
                sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
         }
      }

    }
}
