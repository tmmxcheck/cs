pipeline {
  environment {
    CONTAINER_REGISTRY = "799597332240.dkr.ecr.us-east-1.amazonaws.com/cloudsummit"
    CONTAINER_REGISTRY_CREDENTIALS = "aws-ecr"
    AWS_ECR_READ_CREDENTIALS = "aws-ecr2"
  }
  agent any
    stages {
      stage('Checkout') {
        steps {
          sh 'echo starting'
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
                    imageName: "799597332240.dkr.ecr.us-east-1.amazonaws.com/cloudsummit:demo3",
                    smartcheckHost: "34.201.46.204:30923",
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
                        malware: 10,
                        vulnerabilities: [
                            defcon1: 0,
                            critical: 0,
                            high: 0,
                        ],
                        contents: [
                            defcon1: 0,
                            critical: 0,
                            high: 3,
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

