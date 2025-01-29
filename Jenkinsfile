pipeline {
  agent any

  tools {
    maven 'Maven 3.9.x'
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
        // https://plugins.jenkins.io/config-file-provider/
        configFileProvider([
          // https://github.com/jenkinsci/config-file-provider-plugin/blob/master/src/main/java/org/jenkinsci/lib/configprovider/model/ConfigFile.java
//           configFile(fileId: 'b3db3631-62ee-40e7-8772-83f29ca51e21', variable: 'MAVEN_SETTINGS') // rsc
          configFile(fileId: '1d7da6d7-c14b-4c6c-81ed-512a6c59ca89', variable: 'MAVEN_SETTINGS') // no/blank settings
        ]) {
          sh 'mvn -B -V -e -U -s $MAVEN_SETTINGS clean package -Pdist -Pindex'
        }
      }
    }

    stage('Policy') {
      steps {
        script {
          nexusPolicyEvaluation(
            enableDebugLogging: true,
            iqStage: 'build',
            iqApplication: 'sandbox-application',
            failBuildOnNetworkError: true,
            iqScanPatterns: [
              [scanPattern: '**/target/sonatype-clm/module.xml'],
              [scanPattern: '**/target/*.jar'],
              [scanPattern: '**/target/*.zip']
            ],
            callflow: [
              enable: true,
              logLevel: 'DEBUG',
              algorithm: 'RTA_PLUS',
              includes: [
                '**/target/jenkins-examples-callflow-*-dist.zip'
              ],
              java: [
                // see: https://adoptium.net/temurin/releases/?arch=aarch64
                tool: 'Java 11',
                options: [
                  '-Xmx2G'
                ],
                properties: [
                  foo: 'bar'
                ]
              ],
              entrypointStrategy: [
                $class: 'NamedStrategy',
                name: 'JAVA_MAIN',
                namespaces: [
                  'org.sonatype.lifecycle.jenkins.examples.callflow'
                ]
              ]
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
