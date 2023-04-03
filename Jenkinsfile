pipeline {
  agent any
  tools { 
        maven 'Maven_3_5_2'  
    }
	
     stages {
        stage ('Clone') {
            steps {
                git branch: 'master', url: "https://github.com/mayur189/devsecops-jenkins-k8s-tf-sast-sca-sonarcloud-snyk-repo.git"
            }
        }

        stage ('Artifactory configuration') {
            steps {
                rtServer (
                    serverId: "ARTIFACTORY_SERVER",
                    url: 'http://mayursecurityclass.jfrog.io/artifactory',
                    credentialsId: CREDENTIALS
                )

                rtMavenDeployer (
                    id: "MAVEN_DEPLOYER",
                    serverId: "ARTIFACTORY_SERVER",
                    releaseRepo: ARTIFACTORY_LOCAL_RELEASE_REPO,
                    snapshotRepo: ARTIFACTORY_LOCAL_SNAPSHOT_REPO
                )

                rtMavenResolver (
                    id: "MAVEN_RESOLVER",
                    serverId: "ARTIFACTORY_SERVER",
                    releaseRepo: ARTIFACTORY_VIRTUAL_RELEASE_REPO,
                    snapshotRepo: ARTIFACTORY_VIRTUAL_SNAPSHOT_REPO
                )
            }
        }

        stage ('Exec Maven') {
            steps {
                rtMavenRun (
                    tool: Maven_3_5, // Tool name from Jenkins configuration
                    pom: 'maven-examples/maven-example/pom.xml',
                    goals: 'clean install -U',
                    deployerId: "MAVEN_DEPLOYER",
                    resolverId: "MAVEN_RESOLVER"
                )
            }
        }

        stage ('Publish build info') {
            steps {
                rtPublishBuildInfo (
                    serverId: "ARTIFACTORY_SERVER"
                )
            }
        }

        stage ('Xray scan') {
            steps {
                xrayScan (
                    serverId: "ARTIFACTORY_SERVER",
                    failBuild: false
                )
            }
        }
    }		
  }

