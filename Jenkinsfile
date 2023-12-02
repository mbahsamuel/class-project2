pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'  // Specify the AWS region
        AWS_ACCESS_KEY_ID     = credentials('aws-access-key')
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-key')
        STACK_NAME = 'desamist-tech'  // Specify the CloudFormation stack name
        TEMPLATE_FILE = 'devops/cf-template.yaml'  // Specify the CloudFormation template file
      //  PARAMETER_FILE = 'parameters.json'  // Specify the CloudFormation parameter file
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/mbahsamuel/class-project2.git'
            }
        }
        stage('AWS STS Stage') {
            steps {
                script {
                    // Define the AWS region you want to work with
                    def awsRegion = 'us-east-1'

                    // The role ARN you want to assume in AWS
                    def roleArn = 'arn:aws:iam::869227219142:role/cloudformation-role'

                    // The session name for your assumed role (can be any unique name)
                    def sessionName = 'JenkinsAssumedRole'

                    // The name of the Jenkins credentials containing the AWS access key
                    def awsAccessKeyId = credentials('aws-access-key')

                    // The name of the Jenkins credentials containing the AWS secret key
                    def awsSecretAccessKey = credentials('aws-secret-key')

                    // Step 1: Get AWS temporary credentials using STS
                    def stsCredentials = withAWS(region: awsRegion, accessKeyId: awsAccessKeyId, secretAccessKey: awsSecretAccessKey) {
                        sh "aws sts assume-role --role-arn ${roleArn} --role-session-name ${sessionName} > sts.json"
                        return readJSON(file: 'sts.json')
                    }

                    // Extract temporary AWS credentials
                    def accessKeyId = stsCredentials.Credentials.AccessKeyId
                    def secretAccessKey = stsCredentials.Credentials.SecretAccessKey
                    def sessionToken = stsCredentials.Credentials.SessionToken

                    // Step 2: Use the assumed role credentials to perform AWS actions
                    withAWS(region: awsRegion, accessKeyId: accessKeyId, secretAccessKey: secretAccessKey, sessionToken: sessionToken) {
                        // Your AWS actions go here
                   // sh "aws cloudformation deploy --template-file ${TEMPLATE_FILE} --stack-name ${STACK_NAME} --capabilities CAPABILITY_IAM --region ${AWS_REGION} --s3-bucket desamist001 --s3-prefix cf-templates/v77 --parameter-overrides pHostName1=iwsconapp31d.amtrak.ad.nrpc pHostName2=iwsconpp32d.amtrak.ad.nrpc pDBCluster=d3 --region us-east-1"
                    sh 'aws cloudformation delete-stack --stack-name ${STACK_NAME}  --region ${AWS_REGION}'
                    }
                }
            }
        }

    } 	 	
}
//sh "aws cloudformation deploy --no-fail-on-empty-changeset --capabilities CAPABILITY_IAM CAPABILITY_NAMED_IAM --stack-name ${STACK_NAME} --template-file devops/cf-template.yaml --s3-bucket io-zos-dev-d-128211541887-us-east-1 --s3-prefix cf-templates/v77 --parameter-overrides pHostName1=iwsconapp31d.amtrak.ad.nrpc pHostName2=iwsconpp32d.amtrak.ad.nrpc pHostName3=iwsconapp33d.amtrak.ad.nrpc pDBCluster=d3 --region us-east-1"