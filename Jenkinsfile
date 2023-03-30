pipeline {

    agent {
        docker {
            image 'budtmo/docker-android' //cimg/android:2023.0
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

   stage('Build1') {
                                    steps {
                                    sh 'echo "no" | avdmanager create avd --name test --device "Nexus 5X" --package "system-images;android-30;google_apis;x86_64"'
                                    sh 'emulator -avd Samsung Galaxy S6 -no-audio -no-window -no-boot-anim -gpu off &'
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
