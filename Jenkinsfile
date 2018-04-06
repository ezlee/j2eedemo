node {
   // Mark the code checkout 'stage'....
   stage 'Checkout'

   // Get some code from a GitHub repository
   git url: 'https://github.com/ezlee/j2eedemo.git'

   // Get the maven tool.
   // ** NOTE: This 'M3' maven tool must be configured
   // **       in the global configuration.
   def mvnHome = tool 'M3'

   // Mark the code build 'stage'....
   stage 'Build'
   // Run the maven build
   sh "${mvnHome}/bin/mvn clean install"

   stage name: 'Production', concurrency: 1
            withCredentials([[$class: 'StringBinding',
                              credentialsId: 'ARTIFACTORY_JENKINS_API_KEY',
                              variable: 'ARTIFACTORY_JENKINS_API_KEY']]) {


artifactoryUrl = "http://127.0.0.1:8081/artifactory/"
repo =  "Demo-repo"

def address = artifactoryUrl + repo
def conn = address.toURL().openConnection()
conn.setRequestProperty("X-JFrog-Art-Api", "AKCp2Vob4bTik2Kk48gM7HbCDMSrEW4WnTmxdzqBrFSKNhipSHwo7nFjFC4aogB7wo9L6GzZt")

snapshots_list = []
if (conn.responseCode == 200) {
output = conn.content.text
records = output.split('\n')
snapshots_list = []
    for (record in records) {
     matcher = (record =~ />([0-9\.]+[0-9])/)
      if (matcher) {
            println matcher[0][1]
            snapshots_list.push(matcher[0][1])
        }
    }
} else {
  print "Failed"
}

def sorted = snapshots_list.sort(false) { a, b -> 
List verA = a.tokenize('.')
List verB = b.tokenize('.')
  
def commonIndices = Math.min(verA.size(), verB.size())
  for (int i = 0; i < commonIndices; ++i) {
    def numA = verA[i].toInteger()
    def numB = verB[i].toInteger()
     if (numA != numB) {
       return numA <=> numB
     }
   }
  
  // If we got this far then all the common indices are identical, so whichever version is longer must be more recent
  verA.size() <=> verB.size()
}.reverse()

return sorted
}
}
