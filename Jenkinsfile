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
                           artifactDir: "ocp"
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
                timeout(time:15, unit:'MINUTES') {
                    input message: "Approve Deployment?", ok: "Deploy!"
                }
            }
        }
        stage('Deploy Container') {
            steps {
                script {
                    openshift.withCluster() {
                        openshift.withProject() {
                            dc = openshift.selector("dc", "java")
                            dc.rollout().latest()
                            timeout(10) {
                                dc.rollout().status("-w")
                            }
                        }
                    }

                }
            }
        }
    }
}
