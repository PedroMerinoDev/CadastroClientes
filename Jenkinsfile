pipeline {

    agent {
        docker {
             image 'budtmo/docker-android-x86-8.1' //cimg/android:2023.0
             args '-v $HOME/.android:/root/.android -p 6080:6080 -p 5554:5554 -p 5555:5555 -e DEVICE="Samsung Galaxy S6"' // Mounting local Android configuration directory and mapping ports for emulator
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

   stage('Setup') {
            steps {
                sh 'sdkmanager --install "system-images;android-29;google_apis;x86" "platform-tools" "platforms;android-29" "build-tools;29.0.3"'
                sh 'echo no | avdmanager create avd -n test -k "system-images;android-29;google_apis;x86" --force'
            }
        }
        stage('Start Emulator') {
            steps {
                sh 'emulator -avd test -no-audio -no-window -gpu swiftshader_indirect &'
                // Wait for the emulator to start up
                sh 'android-wait-for-emulator'
                // Unlock the emulator screen
                sh 'adb shell input keyevent 82'
            }
        }
        stage('Build and Test') {
            steps {
                sh './gradlew clean build connectedAndroidTest'
            }
        }


            stage('QualityCheck') {
                    steps {
                      sh "echo $WORKSPACE"// sh "./gradlew lint"
                    }
                  }

                   stage('TestInstrumented') {
                              steps {
                                      sh "echo $WORKSPACE"//sh "./gradlew connectedDebugAndroidTest"
                                   }
                                }


                stage('TestUnit') {
                    steps {
                       sh "echo testee"// sh "./gradlew clean jacocoTestReport"
                    }
                }

                   stage('Build') {
                                    steps {
                                        sh "echo testee"//sh "./gradlew clean bundleRelease"
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
          //junit '**/build/test-results/**/*.xml'
          //jacoco(execPattern: '**/build/jacoco/*.exec')

           sh "rm app/hello.jks"
           sh "rm app/service-account-firebasedist.json"
           sh "rm app/service-account.json"
       }
    }
}
