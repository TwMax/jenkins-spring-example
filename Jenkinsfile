pipeline {
  agent any
  tools {
      maven 'M3'
  }
  stages{
   stage('Build') {
       steps {
         script {
          dir("test")
            {
             sh  'touch $WORKSPACE/Artifact_$BUILD_NUMBER'
            }
            }
          }
        }
    stage('clean install') {
        steps {
            sh "mvn clean install"
            }
        }
         stage ('Upload') {
            steps {
                rtUpload (
                    buildName: JOB_NAME,
                    buildNumber: BUILD_NUMBER,
                    serverId: SERVER_ID, // Obtain an Artifactory server instance, defined in Jenkins --> Manage:
                    spec: '''{
                              "files": [
                                 {
                                  "pattern": "$WORKSPACE/target/*.jar",
                                  "target": "krzysztof/",
                                  "recursive": "false"
                                }
                             ]
                        }'''
                    )
            }
        }
        stage ('Publish build info') {
            steps {
                rtPublishBuildInfo (
                    buildName: JOB_NAME,
                    buildNumber: BUILD_NUMBER,
                    serverId: SERVER_ID
                )

                rtPublishBuildInfo (
                    buildName: JOB_NAME,
                    buildNumber: BUILD_NUMBER,
                    serverId: SERVER_ID
                )
            }
        }
         stage ('Add interactive promotion') {
            steps {
                rtAddInteractivePromotion (
                    //Mandatory parameter
                    serverId: SERVER_ID,

                    //Optional parameters
                    targetRepo: 'krzysztof/',
                    displayName: 'Promote me please',
                    buildName: JOB_NAME,
                    buildNumber: BUILD_NUMBER,
                    comment: 'this is the promotion comment',
                    sourceRepo: 'krzysztof/',
                    status: 'Released',
                    includeDependencies: true,
                    failFast: true,
                    copy: true
                )

                rtAddInteractivePromotion (
                    serverId: SERVER_ID,
                    buildName: JOB_NAME,
                    buildNumber: BUILD_NUMBER
                )
            }
         }
         stage ('Removing files') {
            steps {
                sh 'rm -rf $WORKSPACE/*'
            }
        }


  }
}