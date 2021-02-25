pipeline {
  agent any
  parameters{
    booleanParam(name:"executeStageBuild", defaultValue: true, description:"")
    
  }
  tools{
    maven "Maven3.6.3"
  }
  environment{
    DEMO = true
    BLAZEMETER__CREDENTIALS = credentials('Blazemeter')
    TOMCAT__CREDENTIALS = credentials('tomcat')
    SLACK__CREDENTIALS = credentials('slack-alerts')
    DOCKER__CREDENTIALS = credentials('docker')
  }
  stages {
    stage("build"){
      when{
        expression{
          params.executeStageBuild
        }
      }
      steps{
        echo "Building the application..."
        sh "mvn compile"
      }
    }
    stage("BlazeMeter"){
      when{
        expression{
          DEMO == false
        }
      }
      steps{
        echo "BlazeMeter execution..."
        withCredentials([
          usernamePassword(credentials: "Blazemeter", usernameVariable: USER, passwordVariable: PWD)
        ]){
        }
      }
    }
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
