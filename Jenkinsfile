 pipeline {
    agent any
 
    stages {
        stage('retire scan') {
            steps { 
                sh '''
                    echo "retire started"
                    curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
                    export NVM_DIR="$HOME/.nvm"
                    [ -s "$NVM_DIR/nvm.sh" ] && \\. "$NVM_DIR/nvm.sh"
                    [ -s "$NVM_DIR/bash_completion" ] && \\. "$NVM_DIR/bash_completion"
                    nvm install 14
                    nvm use 14
                    npm install -g retire
                    nvm --version
                    npm --version
                    node --version
                    retire --version
                    npm i
                    echo "Current user: $(whoami)"
                    retire --exitwith 0 --outputformat=json --outputpath=devsecops/retireScan_results.json
                    echo "retire completed"
                '''
            }
        }
        stage('push in defectdojo') {
            steps {
                script {
                    echo 'Defect Started'
                    def buildNumber = currentBuild.getNumber()
                    def timestamp = new Date().format("dd-MM-yyyy_HH:mm:ss", TimeZone.getTimeZone("Asia/Kolkata"))
                    def engagementName = "Engagement-${buildNumber}_Timestamp-${timestamp}"
                    env.ENGAGEMENT_NAME = engagementName

                    echo "Engagement Name: ${engagementName}"

                       sh "curl -X 'POST' \
                        'http://localhost:8080/api/v2/reimport-scan/' \
                        -H 'accept: application/json' \
                        -H 'Authorization: Token bd0cdb143f1e16d506864b7202f99d2ee9b04ca7' \
                        -H 'Content-Type: multipart/form-data' \
                        -H 'X-CSRFTOKEN: uXwXGtseBxqYp8fYhh9E9Yb1OG9QaYt3z3OBzvBT6xuNVf5hmFSViejIKSpyscT3' \
                        -F 'active=true' \
                        -F 'do_not_reactivate=false' \
                        -F 'verified=true' \
                        -F 'close_old_findings=true' \
                        -F 'engagement_name=${engagementName}' \
                        -F 'push_to_jira=false' \
                        -F 'minimum_severity=Info' \
                        -F 'close_old_findings_product_scope=false' \
                        -F 'create_finding_groups_for_all_findings=true' \
                        -F 'tags=retire' \
                        -F 'product_name=Retire_Demo' \
                        -F 'file=@devsecops/retireScan_results.json;type=application/json' \
                        -F 'auto_create_context=true' \
                        -F 'scan_type=Retire.js Scan'"
                    echo 'Defect Completed'
                }
            }
        }
    }
}
