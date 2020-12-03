pipeline {
  environment {
    CONTAINER_REGISTRY = "799597332240.dkr.ecr.us-east-1.amazonaws.com"
    CONTAINER_REGISTRY_CREDENTIALS = "aws-ecr"
    AWS_ECR_READ_CREDENTIALS = "aws-ecr2"
    DOCKER_IMAGE_NAME = "cloudsummit"
  }
  agent any
    stages {
      stage('Checkout') {
        steps {
          sh 'echo starting'
        }
      }
      stage('Build Image') {
        steps {
          script {
            dockerImage = docker.build('$DOCKER_IMAGE_NAME:$BUILD_NUMBER')
          }
        }
      }
      stage("Push Image") {
        steps{
          script {
            docker.withRegistry('https://$CONTAINER_REGISTRY', 'ecr:us-east-1:aws-ecr' ) {
              dockerImage.push()
            }
          }
        }
      }
      stage('SmartCheck') {
        steps {
            withCredentials([
                usernamePassword([
                    credentialsId: AWS_ECR_READ_CREDENTIALS,
                    usernameVariable: "ACCESS_KEY_ID",
                    passwordVariable: "SECRET_ACCESS_KEY",
                    ])
                ]){
                smartcheckScan([
                    imageName: "$CONTAINER_REGISTRY/$DOCKER_IMAGE_NAME:$BUILD_NUMBER",
                    smartcheckHost: "rhdocker1.t1.aws.demorocks:30923",
                    insecureSkipTLSVerify: true,
                    smartcheckCredentialsId: "smartcheck-auth",
                    imagePullAuth: new groovy.json.JsonBuilder([
                        aws: [
                                region: "us-east-1",
                                accessKeyID: ACCESS_KEY_ID,
                                secretAccessKey: SECRET_ACCESS_KEY
                            ]
                    ]).toString(),
                    findingsThreshold: new groovy.json.JsonBuilder([
                        malware: 0,
                        vulnerabilities: [
                            defcon1: 0,
                            critical: 0,
                            high: 0,
                        ],
                        contents: [
                            defcon1: 0,
                            critical: 0,
                            high: 0,
                        ],
                        checklists: [
                            defcon1: 0,
                            critical: 0,
                            high: 0,
                        ],
                    ]).toString(),
                ])
                }
        }
      }
    }
}




