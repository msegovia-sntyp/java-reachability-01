pipeline {
  agent any

  tools {
    maven 'Maven 3.9'
    jdk 'Java 17'
  }

  options {
    buildDiscarder logRotator(
      artifactDaysToKeepStr: '',
      artifactNumToKeepStr: '',
      daysToKeepStr: '1',
      numToKeepStr: '5'
    )
  }

  stages {
    stage('Build') {
      steps {
        sh 'mvn -B -V -e -U clean package -Pdist -Pindex'
      }
    }

    stage('Policy') {
      steps {
        script {
          nexusPolicyEvaluation(
            enableDebugLogging: false,
            iqStage: 'Release',
            iqApplication: 'JavaTestApp1',
            failBuildOnNetworkError: true,
            iqScanPatterns: [
              [scanPattern: '**/target/*.jar'],
              [scanPattern: '**/target/*.zip']
            ],
            reachability: [
                        javaAnalysis: [enable: true]
                ],
                namespaces: [
                  [namespace: 'org.sonatype.lifecycle.jenkins.examples.callflow']
                ]
              )
            ]
          )

          archiveArtifacts(
            artifacts: '**/bomxray.log',
            followSymlinks: false
          )
        }
      }
    }
  }
}
