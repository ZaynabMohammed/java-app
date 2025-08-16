// using ECR For final Project

@Library('shared-lib')_

pipeline {

    agent any

    tools {
        git 'git'
        maven 'Mvn'
		docker 'docker-v1.0'
    }
    environment {
        IMAGE_TAG = "${env.BUILD_NUMBER}"
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
                    dockerBuildApp.dockerBuild(IMAGE_NAME, env.BUILD_NUMBER)
                }
            }
        }
        stage('Docker Push') {
            steps {
		script {
		def dockerPushApp=new org.iti.dockerPush()
		
                dockerPushApp.dockerPush(IMAGE_NAME, env.BUILD_NUMBER, env.DOCKER_USER, env.DOCKER_PASS)
            }
        }
    }
        stage('Deploy to Argo') {
            steps {
                sh """
		git config user.name 'ZaynabMohammed'
		git config user.email 'zeinabmohammed817@gmail.com'
		git config user.password ${GIT_PASS}
		
                ls
                rm -rf Java-App-ArgoCD
                
                git clone 
                git checkout fp
                pwd
                ls
                cd Java-App-ArgoCD
                pwd
                ls
                sed -i "s|image: .*|image: ${IMAGE_NAME}:v${IMAGE_TAG}|" deployment.yml

                git add .
                git commit -m "update image"
                git push
                """
                }
            }
        }
       
}

