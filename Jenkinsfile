pipeline {

    agent {
        docker {
            image 'budtmo/docker-android-x86-8.1'
        }
    }
    /* agent { label 'mac' } */

    environment {
        branch = 'master'
        url = 'https://github.com/PedroMerinoDev/CadastroClientes'
    }

    stages {
        stage('Checkout git') {
            steps {
                git branch: branch, credentialsId: 'udemy', url: url
            }
        }

      /*    stage('Lint') {
            steps {
                sh "./gradlew lint"
            }
        }

        stage('Test') {
            steps {
                sh "./gradlew test --stacktrace"
            }
        } */


        // Manage Jenkins > Credentials > Add > Secret file or Secret Text
        stage('Credentials') {
            steps {
                withCredentials([file(credentialsId: 'ANDROID_KEYSTORE_FILE', variable: 'ANDROID_KEYSTORE_FILE')]) {
                
                    sh "cp '${ANDROID_KEYSTORE_FILE}' app/hello.jks"
                    sh "cat app/hello.jks"
                }
                withCredentials([file(credentialsId: 'FIREBASE_SERVICE_ACCOUNT_FILE', variable: 'FIREBASE_SERVICE_ACCOUNT_FILE')]) {
                    sh "cp '${FIREBASE_SERVICE_ACCOUNT_FILE}' app/service-account-firebasedist.json"
                    sh "cat app/service-account-firebasedist.json"
                }
                withCredentials([file(credentialsId: 'SERVICE_ACCOUNT_FILE', variable: 'SERVICE_ACCOUNT_FILE')]) {
                    sh "cp '${SERVICE_ACCOUNT_FILE}' app/service-account.json"
                    sh "cat app/service-account.json"
                }
            }
        }

        stage('Build') {
            steps {
                 sh "./gradlew clean bundleRelease"
            }
        }

        stage('Publish') {
            parallel {
                stage('Firebase Distribution') {
                    steps {
                       sh "echo teste"//sh "./gradlew appDistributionUploadRelease"
                    }
                }

                stage('Google Play...') {
                    steps {
                        sh "echo teste"//sh "./gradlew publishBundle"
                    }
                }
            }
        }
    }

    post {
       always {
           sh "rm app/hello.jks"
           sh "rm app/service-account-firebasedist.json"
           sh "rm app/service-account.json"
       }
    }
}
