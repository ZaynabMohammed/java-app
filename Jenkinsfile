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

        stage('Deploy to Argo') {
            steps {
                sh """
                    git config user.name 'ZaynabMohammed'
                    git config user.email 'zeinabmohammed817@gmail.com'
                    git config credential.helper store
                    echo "https://${GIT_PASS}@github.com" > ~/.git-credentials
                    
                    rm -rf Java-App-ArgoCD

                    git clone https://github.com/your-repo/Java-App-ArgoCD.git
                    cd Java-App-ArgoCD
                    git checkout fp
                    
                    sed -i "s|image: .*|image: ${IMAGE_NAME}:v${IMAGE_TAG}|" deployment.yml

                    git add .
                    git commit -m "update image"
                    git push
                """
            }
        }
    }
}
