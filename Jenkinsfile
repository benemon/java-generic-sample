pipeline {

    // logrotate on builds so we don't fill up our history
    options {
        buildDiscarder(logRotator(numToKeepStr: '3', artifactNumToKeepStr: '3'))
    }

    // it's often easiest to define a global agent for the entire build
    agent {
        label 'maven'
    }

    parameters {
        string(defaultValue: 'binary', description: 'Build Strategy used to create the image - binary, s2i, dockerfile. Defaults to binary.', name: 'BUILD_STRATEGY')
        string(defaultValue: 'https://quay.io/', description: 'Location of the staging registry between OCP environments', name: 'STAGING_REGISTRY')
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
        stage('Container Build') {
            steps {
                sh { 'mvn clean fabric8:deploy -Popenshift' }
                }
            }
        }
    }
}
