node('nodejs') {
  stage('build') {	
	openshift.withCluster(){
	 openshift.withProject(){
		echo "Using project: ${openshift.project()}"
	 }
	}
  }
  stage('deploy') {
      echo "Deploy step"
  }
}
