def templatePath = 'https://raw.githubusercontent.com/sclorg/nodejs-ex/master/openshift/templates/nodejs.json'
def templateName = 'nodejs-app' 

pipeline {
	agent {
		node {
			label 'nodejs'
		}
	}
	options {
		timeout(time: 20, unit: 'MINUTES')
	}
	stages {
		stage('preamble') {
			steps {
				script {
					openshift.withCluster() {
						openshift.withProject(){
							echo "Using project ${openshift.project()}"
						}
					}
				}
			}
		}
		stage('create') {
			steps {
				script {
					openshift.withCluster() {
						openshift.withProject() {
							openshift.newApp(templatePath)
						}
					}
				}
			}
		}
		stage('build') {
			steps {
				script {
					openshift.withCluster() {
						openshift.withProject() {
							def builds = openshift.selector('bc', templateName).related('builds')
							timeout(5) {
								builds.untilEach(1) {
									return (it.object.status.phase == "Complete")
								}
							}
						}
					}
				}
			}
		}
		stage('deploy') {
			steps {
				script {
					openshift.withCluster() {
						openshift.withProject() {
							def rm = openshift.selector('dc', templateName).related('pods').untilEach(1) {
								return (it.object().status.phase == "Running")
							}
						}
					}
				}
			}
		}
		stage('tag') {
			steps {
				script {
					openshit.withCluster() {
						openshift.withProject() {
							openshift.tag("${templateName}:latest", "${templateName}-staging:latest")
						}
					}
				}
			}
		}
		stage('check services') {
			steps {
				script {
					openshift.withCluster() {
						openshift.withProject() {
							def connectedNormalService = openshift.verifyService('nodejs-app')
							def connectedHeadlessService = openshift.verifyService('nodejs-app-headless')
							if (connectedNormalService) {
								echo "Able to connect to nodejs-app"
							} else {
								echo "Unable to connect to nodejs-app"
							}

							if (connectedHeadlessService) {
								echo "Able to connect to nodejs-app-headless"
							} else {
								echo "Unable to connect to nodejs-app-headless"
							}
						}
					}
				}
			}
		}
	}
}