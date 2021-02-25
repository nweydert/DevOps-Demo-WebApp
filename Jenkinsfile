pipeline {
  agent any
  parameters{
    booleanParam(name:"executeStageBuildWebApp", defaultValue: false, description:"")
    booleanParam(name:"executeStageDeployToQA", defaultValue: false, description:"")
    booleanParam(name:"executeStageStoreArtifacts", defaultValue: true, description:"")
  }
  tools{
    maven "Maven3.6.3"
  }
  
  //environment{
    //BLAZEMETER_CREDENTIALS = credentials('Blazemeter')
    //SLACK_CREDENTIALS = credentials('slack-alerts')
    //DOCKER_CREDENTIALS = credentials('docker')
  //}
  
  stages {
    stage("Build Web App"){
      when{
        expression{
          params.executeStageBuildWebApp
        }
      }
      steps{
        echo "Building the Web Application..."
        sh "mvn compile"
        echo "'Build Web App' - completed successfully."
      }
    }
    stage("Deploy to QA"){
      when{
        expression{
          params.executeStageDeployToQA
        }
      }
      steps{
        echo "Deploying the Web Application to QA..."
        sh "mvn package"
      }
      post{
        success{
          deploy adapters: [tomcat8(url: 'http://172.31.35.77:8080/', credentialsId: "tomcat")], war: '**/*.war', contextPath: '/QAWebapp'
          echo "'Deploy to QA' - completed successfully."
        }
      }
    }
    stage("Store Artifacts"){
      when{
        expression{
          params.executeStageStoreArtifacts
        }
      }
      steps{
        echo "Storing Artifacts..."
        def server = Artifactory.server "https://nweydertartifactory.jfrog.io/artifactory"
        def buildInfo = Artifactory.newBuildInfo()
        buildInfo.env.capture = true
        def rtMaven = Artifactory.newMavenBuild()
        rtMaven.tool = MAVEN_TOOL // Tool name from Jenkins configuration
        rtMaven.opts = "-Denv=dev"
        rtMaven.deployer releaseRepo:'libs-release-local', snapshotRepo:'libs-snapshot-local', server: server
        rtMaven.resolver releaseRepo:'libs-release', snapshotRepo:'libs-snapshot', server: server

        rtMaven.run pom: 'pom.xml', goals: 'package', buildInfo: buildInfo

        buildInfo.retention maxBuilds: 10, maxDays: 7, deleteBuildArtifacts: true
        // Publish build info.
        server.publishBuildInfo buildInfo
      }
    }
    /*
    stage("BlazeMeter"){
      steps{
        echo "BlazeMeter execution..."
        withCredentials([
          usernamePassword(credentials: "Blazemeter", usernameVariable: USER, passwordVariable: PWD)
        ]){
        }
      }
    }
    */
  }
  post{
    always{
      // Message at the end
      echo "Pipeline completed."
    }
    failure{
      echo "An error occurred"
    }
  }
}
