pipeline {
    agent none
    environment {
        // CI set to true to allow it to run in "non-watch" (i.e. non-interactive) mode
        CI = 'true'
//         HOST_IP = "${HOST_IP}"
//         HOST_PORT = "${HOST_PORT}"
    }
    stages {
        stage('Test') {
            parallel {
                stage('Deploy') {
                    agent any
                    steps {        
                        sh 'pip install --upgrade pip'
                        sh 'pip install -r requirements.txt'

                        
                        sh 'sbase install geckodriver'
                        sh 'export PATH=$PATH:/usr/local/lib/python3.9/site-packages/seleniumbase/drivers'

                        
                        sh 'apt-get update && apt-get install firefox-esr -y'

                        // build brand new bagatea-container with bagatea-image
                        sh """docker run -u root -d --name 3x03-con \
                        -v /var/run/docker.sock:/var/run/docker.sock \
                        -v "$HOME":/home \
                        -p 80:80 \
                        3x03-img"""

                        sh 'ls -lha'
                        // sh 'flask run'
                        input message: 'Finished using the web site? (Click "Proceed" to continue)'
                        sh 'pkill -f flask'
                    }
                }
                
                stage('Headless Browser Test') {
                    agent {
                        docker {
                            image '3x03-img:latest'
                        }
                    }
                    steps {
                        sh 'pytest -s -rA --junitxml=logs/report.xml'
                    }
                    post {
                        always {
                            junit testResults: 'logs/report.xml'
                        }
                    }

                }
            }
        }
        // stage('Deliver') {
        //     steps {
        //         script {
        //             try {
        //                 // stop bagatea-container
        //                 sh 'yes | docker stop 3x03-con'
        //             }
        //             catch (Exception e) {
        //                 echo "no container to stop"
        //             }

        //             try {
        //                 // delete bagatea-container
        //                 sh 'yes | docker rm 3x03-con'
        //             }
        //             catch (Exception e) {
        //                 echo "no container to remove"
        //             }
        //         }
                
        //         // build brand new bagatea-container with bagatea-image
        //         sh """docker run -u root -d --name 3x03-con \
        //         -v /var/run/docker.sock:/var/run/docker.sock \
        //         -v "$HOME":/home \
        //         -e VIRTUAL_PORT=5000 \
        //         3x03-img"""
        //     }
        // }
    }
    
//                 -e HOST_IP -e HOST_PORT \
//                 -e VIRTUAL_HOST=bagatea.sitict.net -e VIRTUAL_PORT=5000 \
//     post {
//         always {
//             discordSend description: "${msg}", 
//             successful: currentBuild.resultIsBetterOrEqualTo('SUCCESS'),
//             link: env.BUILD_URL, 
//             result: currentBuild.currentResult, unstable: false, 
//             title: "${JOB_NAME}: #${BUILD_NUMBER}",
//             thumbnail: "https://chpic.su/_data/stickers/n/nanathecharmingcat/nanathecharmingcat_017.webp",
//             webhookURL: "${DISCORD_WEBHOOK}"
//         }
//     }
}
