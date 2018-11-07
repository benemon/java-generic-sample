@Library('reference-steps') _

pipeline {

    // logrotate on builds so we don't fill up our history
    options {
        buildDiscarder(logRotator(numToKeepStr: '3', artifactNumToKeepStr: '3'))
    }

    // it's often easiest to define a global agent for the entire build
    agent {
        label 'maven'
    }

    stages {
        stage('Info') {
            steps {
                script {
                    openshift.withCluster() {
                        openshift.withProject() {
                            echo "Using project: ${openshift.project()}"
                        }
                    }
                }
            }
        }
        stage('Maven Build') {
            steps {
                buildMaven name: "java",
                           artifactDir: "ocp",
                           mavenOpts: "-D skipTests=true"
            }
        }
        stage('Create Container') {
            steps {
                createImage name: "java",
                            artifactDir: "ocp"
                        
            }
        }
        stage('Wait for Deployment Approval') {
            steps {
                getApproval name: "java",
                            waitMinutes: "15"
            }
        }
        stage('Deploy Container') {
            steps {
                deployImage name: "java"
            }
        }
    }
}
