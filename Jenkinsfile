pipeline{
      agent any

      parameters{
            booleanParam(name: 'RELEASE', defaultValue: false, description: 'Is this a Release Candidate?')
      }

      environment {
            INT_VERSION = '0.1.0'
            RELEASE_VERSION = 'R.2'
      }

      stages {
            stage('Audit tools'){
                  steps {
                        auditTools()
                        
                  }
            }

       

            stage('Build'){
                 environment {
                        VERSION_SUFFIX = getBuiltVersion()
                  }

                  steps{
                        echo "Building version: ${INT_VERSION} with suffix: ${VERSION_SUFFIX}"
                        echo "${VERSION_SUFFIX}"
                        paclageApp()
                  }
            }

            stage('Publish'){
                  when {
                        expression { return params.RELEASE}
                  }
                  steps{
                        archiveArtifacts('**/*.war')
                  }
            }


      }

}

void auditTools(){
      sh '''
            git --version
            java --version
            mvn --version   
            '''
}

String getBuiltVersion(){
      if (params.RELEASE){
            return env.RELEASE_VERSION
      }
      else {
            return env.INT_VERSION 
      }
}

void paclageApp(){
      sh """
      mvn versions:set -DnewVersion=${VERSION_SUFFIX}-SNAPSHOT
      mvn versions:update-child-modules
      mvn clean package
      """
}
