/**
 * Utilities methods
 */
// Context vars
context = [:]
context.camundaVersion='7.15.0'
context.gcpRegistry = "localRegistry"

def prettyLog(String logString) {
    def log = """
######################
# ${logString}
######################
"""
    println log
}

def buildFor() {
    def matcher = env.BRANCH_NAME =~ /^(bugfix\/.*|develop|feature\/.*|hotfix\/.*|master)$/

    if (matcher.size() > 0) {
        return true
    }
    return false
}

pipeline {
    agent {
        label 'DOCKER'
    }
    options {
        disableConcurrentBuilds()
        buildDiscarder(logRotator(numToKeepStr: '10'))
    }

    stages {
        stage('checkout') {
            when { expression { return buildFor() } }
            steps {
                checkout scm
            }
        }
        stage('pulling and tagging image') {
            when { expression { return buildFor() } }
            steps {
                script {
                    prettyLog("Pulling camunda image ...")
                    sh "docker pull camunda/camunda-bpm-platform:${context.camundaVersion}"

                    prettyLog("Tagging camunda image ...")

                    sh "docker tag camunda/camunda-bpm-platform:${context.camundaVersion} ${context.gcpRegistry}:${context.camundaVersion}"
                    camundaImage = docker.image("${context.gcpRegistry}:${context.camundaVersion}")
                  
                    prettyLog("Tag created successfully ...")
                }
            }
        }
    }
}
