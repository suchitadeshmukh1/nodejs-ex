node('nodejs') {
  stage 'build'
  openshiftBuild(buildConfig: 'nodejs-ex', showBuildLogs: 'true')
  openshift.withCluster()
    openshift.withProject()
      echo "Using project: ${openshift.project()}"
  stage 'deploy'
  openshiftDeploy(deploymentConfig: 'nodejs-ex')
}
