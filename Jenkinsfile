def COLOR_MAP = [
    'SUCCESS': 'good', 
    'FAILURE': 'danger',
]

pipeline {
    agent any
    
    environment {
        NEXUSPASS = credentials('nexuspass')
    }

    stages {
        stage('Setup parameters') {
            steps {
                script { 
                    properties([
                        parameters([
                            string(
                                defaultValue: '', 
                                name: 'BUILD', 
                            ),
							string(
                                defaultValue: '', 
                                name: 'TIME', 
                            )
                        ])
                    ])
                }
            }
		}

        stage('Ansible Deploy to staging'){
            steps {
                ansiblePlaybook([
                    inventory   : 'ansible/prod.inventory',
                    playbook    : 'ansible/site.yml',
                    installation: 'ansible',
                    colorized   : true,
                    credentialsId: 'app01login',
                    disableHostKeyChecking: true,
                    extraVars   : [
                        USER: "admin",
                        PASS: "${NEXUSPASS}",
                        nexusip: "172.31.88.91",
                        reponame: "vprofile-release",
                        groupid: "QA",
                        time: "${env.TIME}",
                        build: "${env.BUILD}",
                        artifactid: "vproapp",
                        vprofile_version: "vproapp-${env.BUILD}-${env.TIME}.war"
                    ]
                ])
            }
        }
    }

    post {
        always {
            echo 'Slack Notifications.'
            slackSend channel: '#vprofilecicd',
                color: COLOR_MAP[currentBuild.currentResult],
                message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"
        }
    }
}
