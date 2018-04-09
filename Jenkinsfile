node {
    def server = Artifactory.newServer url: 'http://127.0.0.1:8081/artifactory', credentialsId: 'JFROG-ADMIN-PASSWORD'
    def rtMaven = Artifactory.newMavenBuild()
    def buildInfo

    stage ('Clone') {
        git url: 'https://github.com/ezlee/j2eedemo.git', branch: 'jfrog-api'
    }

    stage ('Artifactory configuration') {
        rtMaven.tool = 'M3' // Tool name from Jenkins configuration
        rtMaven.deployer releaseRepo: 'Demo-repo', server: server
        rtMaven.resolver releaseRepo: 'maven2-remote', server: server
        buildInfo = Artifactory.newBuildInfo()
    }

    stage ('Exec Maven') {
        rtMaven.run pom: 'pom.xml', goals: 'clean install', buildInfo: buildInfo
    }

    stage ('Publish build info') {
        server.publishBuildInfo buildInfo
    }
}

