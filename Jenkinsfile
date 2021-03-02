pipeline {
    agent { label 'master' }
    stages {
        stage('Deploy') {
            steps {
                script {
                    sh 'ansible-playbook ./hello_world.yml -i /etc/ansible/inventory -u ${USER} --tags "create"' 
                }
            }
        }
        stage('Test') {
            environment {
                RESPONSE = sh (
                        script: 'curl -Is http://${REMOTE_NODE} | head -n 1',
                        returnStdout: true
                        ).trim()
            }
            steps {
                script {
                    if (env.RESPONSE == 'HTTP/1.1 200 OK') {
                        echo "Reponse code was 200"
                    } else {
                        echo 'Response code was not expected value'
                        exit 0
                    }

					ISSUE = [fields: [ project: [key: "${JIRA_PROJECT}"],
							summary: "Issue encountered in build ${currentBuild.number}",
							description: "JIRA issue created from Jenkins build ${currentBuild.number}",
							issuetype: [name: "Issue"]]]

					RESPONSE = jiraNewIssue issue: ISSUE, site: "${JIRA_CLOUD}"

					echo RESPONSE.successful.toString()
					echo RESPONSE.data.toString()
					
                }
            }
        }
        stage('Cleanup') {
            steps {
                script {
                    sh 'ansible-playbook ./hello_world.yml -i /etc/ansible/inventory -u ${User} --tags "remove"'
                }
            }
        }
    }
}