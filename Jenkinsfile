def registry = 'https://projectd.jfrog.io'
pipeline {
    agent {
        node{
            label 'maven'
        }
    }
environment{
  PATH = "/opt/apache-maven-3.9.6/bin:$PATH"
}
    stages {
        stage("build") {
            steps {
                 echo "----------- build started ----------"
                sh 'mvn clean deploy -Dmaven.test.skip=true'
                 echo "----------- build complted ----------"
            }
        }
        stage("test"){
            steps{
                echo "----------- unit test started ----------"
                sh 'mvn surefire-report:report'
                 echo "----------- unit test Complted ----------"
            }
        }  
 /*   stage('SonarQube analysis') {
          environment{
          scannerHome = tool 'project-sonar-scanner'
        }
        steps{
            withSonarQubeEnv('sonarqube-server'){
                sh "${scannerHome}/bin/sonar-scanner"
            }
        }
    } */
     stage("Jar Publish") {
        steps {
            script {
                    echo '<--------------- Jar Publish Started --------------->'
                     def server = Artifactory.newServer url:registry+"/artifactory" ,  credentialsId:"artifact-cred"
                     def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}";
                     def uploadSpec = """{
                          "files": [
                            {
                              "pattern": "jarstaging/(*)",
                              "target": "projectd-libs-release-local/{1}",
                              "flat": "false",
                              "props" : "${properties}",
                              "exclusions": [ "*.sha1", "*.md5"]
                            }
                         ]
                     }"""
                     def buildInfo = server.upload(uploadSpec)
                     buildInfo.env.collect()
                     server.publishBuildInfo(buildInfo)
                     echo '<--------------- Jar Publish Ended --------------->'  
            
            }
        }   
    }
    }
}
