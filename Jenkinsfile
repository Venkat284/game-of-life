pipeline{
    agent any
    tools{
     jdk "java-1.8"
     
     maven "Maven -3.3.9"

  }
  stages{
      stage('Clone sources'){
       steps{
          git url : 'https://github.com/Venkat284/game-of-life.git'
      }
      }
//sonarqube
      stage('SonarQube analysis'){
          steps{
              //prepare sonarqube scanner enviornment
              withSonarQubeEnv('SonarQube6.7.7'){
                  bat 'mvn org.sonarsource.scanner.maven-plugin:4.2.0.1873:sonar'

              }
          }

      }
      //artifacory configuration
      stage('Artifactory configuration'){
          steps{
              script{
                  rtMaven.tool = 'maven'//maven tool name specified in jenkins configuraion
                  rtMaven.deployer releaseRepo: 'libs-release-local', snapshotRepo : 'libs-snapshot-local',server:server//defining where the build artifacats should be deployed to
                  rtMaven.resolver releaseRepo: 'libs-release', snapshotRepo : 'libs-snapshot',server:server//defining where maven build  should download its dependencies from 
                  rtMaven.deployer.artifactDeploymentpatterns.addExclude("pom.xml")//exclude artifacts from being deployed
                  buildInfo = Artifacory.newBuildInfo() //publishing build-info to artifactory
                  buildInfo.retention maxBuilds : 10, maxDays : 7, deleteBuildArtifacts: true
                  buildInfo.env.capture = true
              }
          }

      }
      //stage 4 executing maven goals
      stage('Execute Maven'){
          steps{
              scrpit{
                  rtMaven.run pom:'pom.xml',goals: 'clean install', buildInfo: buildInfo
              }
          }

      }
      //Stage (v). Publishing Build Information:
      stage('Publish build info'){
           steps{
               script{
                   server.publishBuildInfo buildInfo
               }
               
           }
      }

  }

}

