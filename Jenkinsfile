pipeline{
    agent any

    tools {
        jdk 'Java17'
        maven 'Maven3'
    }
    environment {
        MAVEN_OPTS = "-Dmaven.repo.local=${env.WORKSPACE}/.m2/repository"
        SONAR_USER_HOME = "${env.WORKSPACE}/.sonar"
        APP_NAME = "complete-prodcution-e2e-pipeline"
        RELEASE = "1.0.0"
        DOCKER_USER = "sborkar111"
        DOCKER_PASS = 'dockerhub'
        IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
        // JENKINS_API_TOKEN = credentials("JENKINS_API_TOKEN")

    }
    stages{
        stage("Cleanup Workspace"){
            steps {
                cleanWs()
            }

        }
    
        stage("Checkout from SCM"){
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/sumitborkar/complete-prodcution-e2e-pipeline'
            }

        }

        stage("Build Application"){
            steps {
                sh 'mkdir -p $WORKSPACE/.m2/repository'
                sh "mvn clean package"
            }

        }

        stage("Test Application"){
            steps {
                sh "mvn test"
            }

        }
        
        stage("Sonarqube Analysis") {
            steps {
                script {
                    withSonarQubeEnv(credentialsId: 'jenkins-sonarqube-token') {
                        sh 'mkdir -p $WORKSPACE/.sonar/cache'
                        sh "mvn sonar:sonar"
                    }
                }
            }

        // }
        // stage('Build & Push Image with Kaniko') {
        //     agent {
        //         kubernetes {
        //             // This YAML is the Kaniko agent pod spec from above
        //             yaml """
        //             apiVersion: v1
        //             kind: Pod
        //             metadata:
        //             labels:
        //                 jenkins: kaniko-agent
        //             spec:
        //               serviceAccountName: jenkins-admin
        //               securityContext:
        //                 fsGroup: 1050
        //                 runAsUser: 1050
        //               containers:
        //                 - name: kaniko
        //                   image: gcr.io/kaniko-project/executor:debug
        //                   command:
        //                     - cat
        //                   tty: true
        //                   volumeMounts:
        //                     - name: docker-config
        //                       mountPath: /kaniko/.docker
        //                     - name: workspace-volume
        //                       mountPath: /home/jenkins/agent
        //               volumes:
        //                 - name: docker-config
        //                   secret:
        //                     secretName: dockerhub-cred
        //                     items:
        //                     - key: .dockerconfigjson
        //                       path: config.json
        //                 - name: workspace-volume
        //                   emptyDir: {}
        //             """
        //         }
        //     }
        //     stages {
        //         stage('Kaniko Build & Push') {
        //             steps {
        //                 container('kaniko') {
        //                     sh '''
        //                       /kaniko/executor \
        //                         --context=$WORKSPACE \
        //                         --dockerfile=$WORKSPACE/Dockerfile \
        //                         --destination=${IMAGE_NAME}:${IMAGE_TAG} \
        //                         --verbosity=debug
        //                     '''
        //                 }
        //             }
        //         }
        //     }
        // }

        // stage("Quality Gate") {
        //     steps {
        //         script {
        //             waitForQualityGate abortPipeline: false, credentialsId: 'jenkins-sonarqube-token'
        //         }
        //     }

        // }

        // stage("Build & Push Docker Image") {
        //     steps {
        //         script {
        //             docker.withRegistry('',DOCKER_PASS) {
        //                 docker_image = docker.build "${IMAGE_NAME}"
        //             }

        //             docker.withRegistry('',DOCKER_PASS) {
        //                 docker_image.push("${IMAGE_TAG}")
        //                 docker_image.push('latest')
        //             }
        //         }
        //     }

        // }

        // stage("Trivy Scan") {
        //     steps {
        //         script {
		//    sh ('docker run -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image dmancloud/complete-prodcution-e2e-pipeline:1.0.0-22 --no-progress --scanners vuln  --exit-code 0 --severity HIGH,CRITICAL --format table')
        //         }
        //     }

        // }

        // stage ('Cleanup Artifacts') {
        //     steps {
        //         script {
        //             sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
        //             sh "docker rmi ${IMAGE_NAME}:latest"
        //         }
        //     }
        // }


        // stage("Trigger CD Pipeline") {
        //     steps {
        //         script {
        //             sh "curl -v -k --user admin:${JENKINS_API_TOKEN} -X POST -H 'cache-control: no-cache' -H 'content-type: application/x-www-form-urlencoded' --data 'IMAGE_TAG=${IMAGE_TAG}' 'https://jenkins.dev.dman.cloud/job/gitops-complete-pipeline/buildWithParameters?token=gitops-token'"
        //         }
        //     }

        // }

    }

    // post {
    //     failure {
    //         emailext body: '''${SCRIPT, template="groovy-html.template"}''', 
    //                 subject: "${env.JOB_NAME} - Build # ${env.BUILD_NUMBER} - Failed", 
    //                 mimeType: 'text/html',to: "dmistry@yourhostdirect.com"
    //         }
    //      success {
    //            emailext body: '''${SCRIPT, template="groovy-html.template"}''', 
    //                 subject: "${env.JOB_NAME} - Build # ${env.BUILD_NUMBER} - Successful", 
    //                 mimeType: 'text/html',to: "dmistry@yourhostdirect.com"
    //       }      
    // }
}
