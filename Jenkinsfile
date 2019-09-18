pipeline {
    agent {
        label 'aws_machine'
    }

    triggers {
        pollSCM('H * * * *')
    }

    parameters {
        string(name: 'branch', defaultValue: 'master', description: 'Default Branch Name')
        string(name: 'url', defaultValue: '', description: 'Github URL')
    }

    options {
        timeout(120)
        disableConcurrentBuilds()
        buildDiscarder logRotator(artifactDaysToKeepStr: '10', artifactNumToKeepStr: '10', daysToKeepStr: '30', numToKeepStr: '30')
    }
	
    stages {
        stage('Github-Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']],
                doGenerateSubmoduleConfigurations: false, 
                extensions: [[$class: 'CleanCheckout']], 
                submoduleCfg: [], 
                userRemoteConfigs: [[credentialsId: 'aws_id', url: '$url']]])
                }
            }
        }

        stage('Create-CFT') {
            sh 'aws cloudformation create-stack --stack-name myteststack \\
                --template-body ./sampletemplate.json \\
                --parameters ParameterKey=KeyPairName,ParameterValue=TestKey \\
                    ParameterKey=SubnetIDs,ParameterValue=SubnetID1\\,SubnetID2'
        }
    }
}