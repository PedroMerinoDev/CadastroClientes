pipeline {

    agent {
        docker {
            image 'budmo/docker-android:latest' //cimg/android:2023.03 'androidsdk/android-30'
            label 'android'
        }
    }
    /* agent { label 'mac' } */

    environment {
        branch = 'master'
        url = 'https://github.com/PedroMerinoDev/CadastroClientes'
        ANDROID_HOME = 'usr/local/android-sdk'
        EMULATOR_NAME = 'Samsung Galaxy S6'
        TEST_APK_LOCATION = 'app/build/outputs/apk/debug/app-debug-androidTest.apk'
    }

    stages {
        stage('Checkout git') {
            steps {
                git branch: branch, credentialsId: 'udemy', url: url
            }
        }

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


            stage('QualityCheck') {
                    steps {
                      sh "echo testee"// sh "./gradlew lint"
                    }
                  }

                   stage('TestInstrumented') {
                              steps {
                                     sh "./gradlew connectedDebugAndroidTest -Pandroid.testInstrumentationRunnerArguments.emulator='${EMULATOR_NAME}'"
                                   }
                                }


                stage('TestUnit') {
                    steps {
                        sh "./gradlew clean jacocoTestReport"
                    }
                }

                   stage('Build') {
                                    steps {
                                        sh "./gradlew clean bundleRelease"
                                        //step( [ $class: 'JacocoPublisher' ] )
                                    }
                                }


        stage('Publish') {
            parallel {
                stage('Firebase Distribution') {
                    steps {
                       sh "echo testee"//sh "./gradlew appDistributionUploadRelease"
                    }
                }

                stage('Google Play...') {
                    steps {
                        sh "echo testee"//sh "./gradlew publishBundle"
                    }
                }
            }
        }
    }

    post {
       always {
          junit '**/build/test-results/**/*.xml'
          jacoco(execPattern: '**/build/jacoco/*.exec')

           sh "rm app/hello.jks"
           sh "rm app/service-account-firebasedist.json"
           sh "rm app/service-account.json"
       }
    }
}
