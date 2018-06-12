node {
        notify1('Started testing-whaleapp build')
        try {
			stage('Checkout') {
			checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/chilu49/hellowhale.git']]])
			}
 			stage('Build Image And Push') {
			dir('/opt/jenkins/workspace/testing-asdfkljasdf/'){
 						def app = docker.build ("testing-whaleapp:${env.BUILD_ID}")
						}
					}
			stage ('Deploy-dev') {
				//build job: 'account-service-pipeline', wait: false
				sh ''' docker stop testing-whaleapp'''
				sh ''' docker rm testing-whaleapp '''
				sh ''' docker run -d -it --name testing-whaleapp  testing-whaleapp:${BUILD_NUMBER}'''
    
       }    
        notify2('Successfully Deployed testing-whaleapp')  
    }  catch (err) {
        notify1("Error {err}")
        currentBuild.result = 'FAILURE'
    }
}   
@NonCPS
def prevBuildLastCommitId() {
    def prev = currentBuild.getPreviousBuild()
    def items = null
    def result = null
    if (prev != null && prev.changeSets != null && prev.changeSets.size() && prev.changeSets[prev.changeSets.size() - 1].items.length) {
        items = prev.changeSets[prev.changeSets.size() - 1].items
        result = items[items.length - 1].commitId
    }
    return result
}
@NonCPS
def getChangeString() {
    String msg = ""
    String repoUrl = '${env.BUILD_URL}'
    def lastId = null
    def prevId = prevBuildLastCommitId()
    def changeLogSets = currentBuild.changeSets

    for (int i = 0; i < changeLogSets.size(); i++) {
        def entries = changeLogSets[i].items
        for (int j = 0; j < entries.length; j++) {
            def entry = entries[j]
            lastId = entry.commitId
            changeString = changeString + "${commitInfo(entry)}"
        }
    }
    if (prevId != null && lastId != null) {
        changeString = changeString + "\n${repoUrl}/branches/compare/${lastId}..${prevId}#diff\n"
    }
    return msg
}
    
	def notify1(status){
	emailext(
		to: "ravinder.chiluveru@cabelas.com",
		subject: "${status}:' [${env.BUILD_NUMBER}]'",
		body: """<p>${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
			<p>Check console output at <a href='${env.BUILD_URL}'>${env.JOB_NAME}  [${env.BUILD_NUMBER}]</a></p>"""
			)
    }
	def notify2(status){
	emailext(
		to: "ravinder.chiluveru@cabelas.com",
		subject: "${status}: Job ${env.JOB_NAME} [${env.BUILD_NUMBER}]",
		body: "Changes:\n " + getChangeString() + "\n\n<br> Check console output at: $BUILD_URL/console" + "\n"
			)
    }
	def notify3(status){
	emailext(
		to: "ravinder.chiluveru@cabelas.com",
		subject: "${status}:' [${env.BUILD_NUMBER}]'",
		body: "Changes:\n " + getChangeString() + "\n\n<br> Check console output at: $BUILD_URL/console" + "\n"

			)
    }
