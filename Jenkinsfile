node {
    def server
    def buildInfo
    def rtMaven

    stage ('Clone') {
        git url: 'https://github.com/ezlee/j2eedemo.git', branch: 'maven-deploy' 
    }

    stage ('Artifactory configuration') {
        // Obtain an Artifactory server instance, defined in Jenkins --> Manage:
        server = Artifactory.server url: 'http://127.0.0.1:8081/artifactory', credentialsId: 'JFROG-ADMIN-PASSWORD' 

        rtMaven = Artifactory.newMavenBuild()
        rtMaven.tool = M3 // Tool name from Jenkins configuration
        rtMaven.deployer releaseRepo: 'Demo-repo', snapshotRepo: 'Demo-repo', server: server
        rtMaven.resolver releaseRepo: 'maven2-remote', snapshotRepo: 'maven2-remote', server: server
        rtMaven.deployer.deployArtifacts = false // Disable artifacts deployment during Maven run

        buildInfo = Artifactory.newBuildInfo()
    }
 
    stage ('Test') {
        rtMaven.run pom: 'pom.xml', goals: 'clean test'
    }
        
    stage ('Install') {
        rtMaven.run pom: 'pom.xml', goals: 'install', buildInfo: buildInfo
    }
 
    stage ('Deploy') {
        rtMaven.deployer.deployArtifacts buildInfo
    }
        
    stage ('Publish build info') {
        server.publishBuildInfo buildInfo
    }
}
