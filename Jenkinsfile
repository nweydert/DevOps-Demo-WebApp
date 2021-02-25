pipeline {
  agent any
  parameters{
    booleanParam(name:"executeStageBuildWebApp", defaultValue: true, description:"")
    booleanParam(name:"executeStageDeployToQA", defaultValue: true, description:"")
  }
  tools{
    maven "Maven3.6.3"
  }
  
  environment{
    //BLAZEMETER__CREDENTIALS = credentials('Blazemeter')
    TOMCAT__CREDENTIALS = credentials('tomcat')
    //SLACK__CREDENTIALS = credentials('slack-alerts')
    //DOCKER__CREDENTIALS = credentials('docker')
  }
  
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
        deploy adapters: [tomcat8(url: 'http://172.31.35.77:8080/', 
                                  credentialsId: ${TOMCAT__CREDENTIALS})], 
                     war: '**/*.war',
                     contextPath: '/QAWebapp'
        echo "'Deploy to QA' - completed successfully."
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
