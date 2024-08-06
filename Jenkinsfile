pipeline {
    agent any

    environment {
        MAVEN_OPTS = '-Xmx2048m'
        DOCKER_IMAGE = 'maven:3.6.3-jdk-8' // Change to your Maven Docker image if necessary
        // NEXUS_URL = 'http://nexus.example.com/repository/maven-releases/'
        // MAVEN_CREDENTIALS_ID = 'nexus-credentials'
        // GITHUB_CREDENTIALS_ID = 'github-credentials'
        // FORTIFY_MAVEN_PLUGIN = 'true'
        SKIP_TESTS = 'true'
        SKIP_FUNCTIONAL_TESTS = 'true'
        SKIP_PERFORMANCE_TESTS = 'true'
        SKIP_SONAR = 'true'
        SKIP_FORTIFY = 'true'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/develop']],
                          userRemoteConfigs: [[url: 'https://github.com/gouki001/Mulesoftapi.git', credentialsId: env.GITHUB_CREDENTIALS_ID]]])
            }
        }

        stage('Build') {
            steps {
                script {
                    docker.image(env.DOCKER_IMAGE).inside {
                        sh 'mvn clean package -DskipTests=${SKIP_TESTS}'
                    }
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    if (env.SKIP_TESTS == 'false') {
                        docker.image(env.DOCKER_IMAGE).inside {
                            sh 'mvn test'
                        }
                    } else {
                        echo 'Tests are skipped.'
                    }
                }
            }
        }

    //     stage('Fortify Scan') {
    //         when {
    //             expression { return env.SKIP_FORTIFY == 'false' }
    //         }
    //         steps {
    //             script {
    //                 docker.image(env.DOCKER_IMAGE).inside {
    //                     sh 'mvn com.fortify.ps.maven.plugin:sca-maven-plugin:clean compile'
    //                 }
    //             }
    //         }
    //     }

    //     stage('SonarQube Analysis') {
    //         when {
    //             expression { return env.SKIP_SONAR == 'false' }
    //         }
    //         steps {
    //             script {
    //                 docker.image(env.DOCKER_IMAGE).inside {
    //                     sh 'mvn sonar:sonar'
    //                 }
    //             }
    //         }
    //     }

    //     stage('Deploy to Nexus') {
    //         steps {
    //             script {
    //                 docker.image(env.DOCKER_IMAGE).inside {
    //                     withCredentials([usernamePassword(credentialsId: env.MAVEN_CREDENTIALS_ID, usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
    //                         sh 'mvn deploy -DrepositoryId=nexus -Durl=${NEXUS_URL}'
    //                     }
    //                 }
    //             }
    //         }
    //     }
    // }

    post {
        always {
            archiveArtifacts artifacts: '**/target/*.jar', allowEmptyArchive: true
            junit 'target/surefire-reports/*.xml'
        }
        success {
            echo 'Build succeeded!'
        }
        failure {
            echo 'Build failed!'
        }
    }
}
