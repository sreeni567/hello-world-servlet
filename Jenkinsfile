pipeline {
   agent any

   tools {
      // Install the Maven version configured as "Maven-3.6" and add it to the path.
      maven "Maven-3.6"
   }

   stages {
      stage('Checkout') {
         steps {
           checkout([$class: 'GitSCM', branches: [[name: '*/devops']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/sreeni567/hello-world-servlet.git']]])
         }
       }
stage('Unit Test cases') {
         steps {
           sh label: '', script: 'clean test'
         }
       }
stage('Code quality/Sonarqube') {
        environment {
        scannerHome = tool 'sonarqube'
    }
    steps {
        withSonarQubeEnv('sonarqube') {
            sh "${scannerHome}/bin/sonar-scanner"
        }
        timeout(time: 10, unit: 'MINUTES') {
            waitForQualityGate abortPipeline: true
        }
         }
       }
stage('Package creation') {
         steps {
           sh label: '', script: 'clean package'
         }
       }

stage('Artifact upload') {
         steps {
           nexusPublisher nexusInstanceId: '1234', nexusRepositoryId: 'releases', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '', filePath: '/var/lib/jenkins/workspace/helloworld-pipeline/target/helloworld.war']], mavenCoordinate: [artifactId: 'hello-world-servlet-example', groupId: 'com.geekcap.vmturbo', packaging: 'war', version: '1.0']]]
         }
       }
         post {
            // If Maven was able to run the tests, even if some of the test
            // failed, record the test results and archive the jar file.
            success {
               junit '**/target/surefire-reports/TEST-*.xml'
               archiveArtifacts 'target/*.jar'
            }
         }
      
   }
}
