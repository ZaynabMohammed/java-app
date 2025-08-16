// Using ECR for Final Project

@Library('shared-lib') _

pipeline {
    agent any

    tools {
        git 'git'
        maven 'Mvn'
        dockerTool 'docker-v1.0'
    }

    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
        IMAGE_NAME = "zeinab817/app-java"
        DOCKER_USER = credentials('docker-username')
        DOCKER_PASS = credentials('docker-password')
        GIT_PASS = credentials('github-password')
    }

    stages {
        stage('Build') {
            steps {
                script {
                    def mavenBuild = new org.iti.mvn()
                    mavenBuild.javaBuild("package install")
                }
            }
        }

        stage('Archive') {
            steps {
                script {
                    def archiveApp = new org.iti.archiveApp()
                    archiveApp.archive()
                }
            }
        }

        stage('Docker Build') {
            steps {
                script {
                    def dockerBuildApp = new org.iti.dockerBuild()
                    dockerBuildApp.dockerBuild(IMAGE_NAME, IMAGE_TAG)
                }
            }
        }

        stage('Docker Push') {
            steps {
                script {
                    def dockerPushApp = new org.iti.dockerPush()
                    dockerPushApp.dockerPush(IMAGE_NAME, IMAGE_TAG, DOCKER_USER, DOCKER_PASS)
                }
            }
        }

      stage("Update ArgoCD manifest") {
           steps {
                script {
                // Clone the ArgoCD repo
                sh "mkdir -p argocd"
                dir('argocd') {
                    checkout([
                        $class: 'GitSCM',
                        branches: [[name: '*/main']],
                        extensions: [],
                        userRemoteConfigs: [[
                            url: 'https://github.com/ZaynabMohammed/argocd-java.git',
                            credentialsId: 'GIT_CRED'  // Jenkins credentia user name witj pass
                        ]]
                    ])
 
                    // Update the image tag in deployment.yaml
                    sh "sed -i 's#        image: .*#        image: ${env.IMAGE_NAME}:${IMAGE_TAG}#' deployment.yaml"
                    sh "cat deployment.yaml"
                    sh "ls"
                    sh "git add ."
                    sh "git commit -m 'update Image'"
                // Commit and push changes
                    withCredentials([usernamePassword(
                        credentialsId: 'GIT_CRED',
                        usernameVariable: 'GIT_USER',
                        passwordVariable: 'GIT_TOKEN'
                )]) {
                        sh """
                            git remote set-url origin https://${GIT_USER}:${GIT_TOKEN}@github.com/ZaynabMohammed/argocd-java.git
                            git push origin HEAD:main
                            """
                }
                }
            }
    }
}
