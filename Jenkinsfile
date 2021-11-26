pipeline {
    agent any

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "M3"
    }

    stages {
        stage('Build') {
            steps {
                sh "mvn clean compile"
            }
        }
        stage('Test') {
            steps {
                sh "mvn test"
            }
        }
        stage('Build-artifactory') {
               steps {
                 script {
                  dir("test")
                    {
                     sh  'touch $WORKSPACE/Artifact_$BUILD_NUMBER'
                    }
                    }
                  }
                }
        stage('Upload-Artifactory') {
            steps {
                rtUpload(
                    buildName: JOB_NAME,
                    buildNumber: BUILD_NUMBER,
                    serverId: SERVER_ID,
                    spec: '''{
                                "files": [
                                    {
                                    "pattern": "$WORKSPACE/Demo-Artifactory/Artifact_*",
                                    "target": "krzysztof/",
                                    "recursive": "false"
                                    }
                               ]
                    }'''
                )
            }
        }
        stage('Publish build info') {
            steps {
                rtPublishBuildInfo (
                    buildName: JOB_NAME,
                    buildNumber: BUILD_NUMBER,
                    serverId: SERVER_ID
                    )

//                 rtPublishBuildInfo (
//                     buildName: JOB_NAME,
//                     buildNumber: BUILD_NUMBER,
//                     serverId: SERVER_ID
//                     )
            }
        }

        stage('Add interactive promotion') {
            steps {
                rtAddInteractivePromotion (
                    serverId: SERVER_ID,
                    targetRepo: 'krzysztof/',
                    displayName: 'Promote me please',
                    buildName: JOB_NAME,
                    buildNumber: BUILD_NUMBER,
                    comment: 'this is promotion comment',
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
    }
}
