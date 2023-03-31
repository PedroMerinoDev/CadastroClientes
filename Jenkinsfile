pipeline {

    agent {
        docker {
             image 'androidsdk/android-30' //cimg/android:2023.0
             args '--privileged'
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

        stage('Build Prepare') {
           steps {
                sh "echo teste" //sh "./gradlew clean"
           }
        }

        stage('QualityCheck') {
           steps {
                sh "echo teste" //sh "./gradlew lint"
           }
        }


       /*    stage('Install KVM') {
            steps {
                sh 'apt-get update && apt-get install -y qemu-kvm libvirt-bin ubuntu-vm-builder bridge-utils'
                sh 'kvm-ok'
            }
        }

       stage('Create Emulator') {
           steps {
               sh 'sdkmanager --install "system-images;android-30;google_apis;x86" "platform-tools" "platforms;android-30" "build-tools;29.0.3"'
               sh 'echo no | avdmanager create avd --name test --package "system-images;android-30;google_apis;x86_64"'
           }
       }

       stage('Start Emulator') {
           steps {
               sh 'emulator -avd test -no-audio -no-window -gpu swiftshader_indirect -qemu -m 2048 -enable-kvm & adb wait-for-device'
           }
       } */

       stage('Tests') {
           steps {
               sh "./gradlew clean jacocoTestReport"
           }
       }

       stage('Assign Build') {
           steps {
                sh "echo teste" //sh "./gradlew clean bundleRelease"
               //step( [ $class: 'JacocoPublisher' ] )
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
                        sh "echo teste" //sh "./gradlew publishBundle"
                    }
                }
            }
        }
    }

    post {
       always {
          junit '**/build/test-results/**/*.xml'
          //junit '**/build/reports/lint-results.xml'
          //jacoco(execPattern: '**/build/jacoco/*.exec')

           sh "rm app/hello.jks"
           sh "rm app/service-account-firebasedist.json"
           sh "rm app/service-account.json"
       }
    }
}
