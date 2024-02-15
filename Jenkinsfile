//@Library('my-shared-library') _

pipeline{

    agent any
    //agent { label 'Demo' }

    parameters{

        choice(name: 'action', choices: 'create\ndelete', description: 'Choose create/Destroy')
        string(name: 'ImageName', description: "name of the docker build", defaultValue: 'javapp')
        string(name: 'ImageTag', description: "tag of the docker build", defaultValue: 'v1')
        string(name: 'DockerHubUser', description: "name of the Application", defaultValue: 'dhani345')
    }

    stages{
         
        stage('Git Checkout'){
                when { expression {  params.action == 'create' } }
            steps{
                
               git branch: 'main', url: 'https://github.com/kdhani/Java_app_3.0/blob/main/Jenkinsfile'
            }
        }
         stage('Unit Test maven'){
         
         when { expression {  params.action == 'create' } }

            steps{
               script{
                   
                   sh 'mvn test'
               }
            }
        }
         stage('Integration Test maven'){
         when { expression {  params.action == 'create' } }
            steps{
               script{
                   
                   sh 'mvn verify -DskipUnitTests'
               }
            }
        }
        stage('Static code analysis: Sonarqube'){
         when { expression {  params.action == 'create' } }
            steps{
               script{
                   
                   sh 'mvn clean package sonar:sonar'
               }
            }
       }
       stage('Quality Gate Status Check : Sonarqube'){
         when { expression {  params.action == 'create' } }
            steps{
               script{
                   
                   def SonarQubecredentialsId = 'sonarqube-api'
                   QualityGateStatus(SonarQubecredentialsId)
               }
            }
       }
        stage('Maven Build : maven'){
         when { expression {  params.action == 'create' } }
            steps{
               script{
                   
                   sh 'mvn clean install -DskipTests'
               }
            }
        }
        stage('Docker Image Build'){
         when { expression {  params.action == 'create' } }
            steps{
               script{
                   
                   sh "docker image build -t dhani345/java_app_3.0:latest ."
               }
            }
        }
         stage('Docker Image Scan: trivy '){
         when { expression {  params.action == 'create' } }
            steps{
               script{
                   
                   sh """trivy image dhani345/java_app_3.0:latest>scan.txt"""
               }
            }
        }
        stage('Docker Image Push : DockerHub '){
         when { expression {  params.action == 'create' } }
            steps{
               script{
                   

                   withCredentials([usernamePassword(
                       credentialsId: "docker",
                       usernameVariable: "USER",
                       passwordVariable: "PASS"
                    )]) {
                        sh "docker login -u '$USER' -p '$PASS'"
                    }
                   
                   
                   sh  "docker login -u '$user' -p '$pass' "
                   sh "docker image push dhani345/java_app_3.0:latest"
               }
            }
        }   
        stage('Docker Image Cleanup : DockerHub '){
         when { expression {  params.action == 'create' } }
            steps{
               script{
                   
                   sh """docker rmi dhani345/java_app_3.0:latest"""
               }
            }
        }      
    }
}
