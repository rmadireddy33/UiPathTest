pipeline {
	    agent any
	

	        //Environment Variables
	        environment {
	        MAJOR = '1'
	        MINOR = '0'
	        //Orchestrator Services
	        UIPATH_ORCH_URL = "https://cloud.uipath.com/"
	        UIPATH_ORCH_LOGICAL_NAME = "rmadireddy33"
	        UIPATH_ORCH_TENANT_NAME = "rmadireddy33"
	        UIPATH_ORCH_FOLDER_NAME = "Default"
	    }
	

	    stages {
	

	        // Printing Basic Information
	        stage('Preparing'){
	            steps {
	                echo "Jenkins Home ${env.JENKINS_HOME}"
	                echo "Jenkins URL ${env.JENKINS_URL}"
	                echo "Jenkins JOB Number ${env.BUILD_NUMBER}"
	                echo "Jenkins JOB Name ${env.JOB_NAME}"
	                echo "GitHub BranhName ${env.BRANCH_NAME}"
	                checkout scm
	

	            }
	        }
	

	         // Build Stages
	        stage('Build') {
	            steps {
	                echo "Building..with ${WORKSPACE}"
	                UiPathPack (
	                      outputPath: "Output\\${env.BUILD_NUMBER}",
	                      projectJsonPath: "project.json",
	                      //version: [$class: 'ManualVersionEntry', version: "${MAJOR}.${MINOR}.${env.BUILD_NUMBER}"],
						  //version: AutoVersion(),
						  version: CurrentVersion(),
	                      useOrchestrator: false
	        )
	            }
	        }
	         // Test Stages
	        stage('Test') {
	            steps {
	                echo 'Testing..the workflow...'
	            }
	        }
	

	         // Deploy Stages
	        stage('Deploy to UAT') {
			when{
			expression{"${BRANCH_NAME}".toUpperCase() == 'DEVELOPMENT'}
			}
	            steps {
	                echo "Deploying ${BRANCH_NAME} to UAT "
	                UiPathDeploy (
	                packagePath: "Output\\${env.BUILD_NUMBER}",
	                orchestratorAddress: "${UIPATH_ORCH_URL}",
	                orchestratorTenant: "${UIPATH_ORCH_TENANT_NAME}",
	                folderName: "${UIPATH_ORCH_FOLDER_NAME}",
	                environments: 'DEV',
	                //credentials: [$class: 'UserPassAuthenticationEntry', credentialsId: 'APIUserKey']
	                credentials: Token(accountName: "${UIPATH_ORCH_LOGICAL_NAME}", credentialsId: 'APIUserKey')
			//credentials: UserPass('pOSC9EqPbB1Ns8i3twmjhRGSGArFpe1I3s4GLWmCgLtiQ')
	
	        )
	            }
	        }
	

	

	         // Deploy to Production Step
	        stage('Deploy to Production') {
			when{
			expression{"${BRANCH_NAME}".toUpperCase() == 'MAIN'}
			}
	            steps {
	                echo 'Deploy to Production'
					  UiPathDeploy (
	                packagePath: "Output\\${env.BUILD_NUMBER}",
	                orchestratorAddress: "${UIPATH_ORCH_URL}",
	                orchestratorTenant: "${UIPATH_ORCH_TENANT_NAME}",
	                folderName: "${UIPATH_ORCH_FOLDER_NAME}",
	                environments: 'DEV',
	                //credentials: [$class: 'UserPassAuthenticationEntry', credentialsId: 'APIUserKey']
	                credentials: Token(accountName: "${UIPATH_ORCH_LOGICAL_NAME}", credentialsId: 'APIUserKey')
			//credentials: UserPass('pOSC9EqPbB1Ns8i3twmjhRGSGArFpe1I3s4GLWmCgLtiQ')
	
	        )
	                }
	            }
				
				
			// Checkout Basic Information
	        stage('Checkout'){
	            steps {
	                echo "Checkout GIT"
					echo "Jenkins Home ${env.JENKINS_HOME}"
	                echo "Jenkins URL ${env.JENKINS_URL}"
	                echo "Jenkins JOB Number ${env.BUILD_NUMBER}"
	                echo "Jenkins JOB Name ${env.JOB_NAME}"
	                echo "GitHub BranhName ${env.BRANCH_NAME}"
	                checkout([$class: 'GitSCM', 
                          branches: [[name: '*/master']], 
                         //doGenerateSubmoduleConfigurations: false, 
                         // extensions: [], 
                          gitTool: 'default', 
                          //submoduleCfg: [], 
                          userRemoteConfigs: [[credentialsId: 'gitcredentials', url: 'https://github.com/rmadireddy33/RPATesting.git']]
                        ])
	

	            }
	        }
	    }
	

	    // Options
	    options {
	        // Timeout for pipeline
	        timeout(time:80, unit:'MINUTES')
	        skipDefaultCheckout()
	    }
	

	

	    // 
	    post {
	        success {
	            echo 'Deployment has been completed!'
	        }
	        failure {
	          echo "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.JOB_DISPLAY_URL})"
	        }
	        always {
	            /* Clean workspace if success */
	            cleanWs()
				echo 'Always'
	        }
	    }
	

	}
