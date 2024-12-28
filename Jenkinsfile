pipeline {
    agent any
    stages {
        stage('Code Quality') {
            steps {
                sh 'cd webapp && sudo docker run --rm -e SONAR_HOST_URL="http://35.86.122.209/:9000" -v ".:/usr/src" -e SONAR_TOKEN="sqp_18e8cd7a5b82ba22ac56ca2d9aceaddc3532a87a" sonarsource/sonar-scanner-cli -Dsonar.projectKey=lms'
            }
        }
        stage('Build') {
            steps {
                sh 'cd webapp && npm install && npm run build'
            }
        }
        stage('Publish LMS') {
            steps {
                script {
                    def packageJson = readJSON file: 'webapp/package.json'
                    def packageJSONVersion = packageJson.version
                    echo "${packageJSONVersion}"
                    sh "zip webapp/lms-${packageJSONVersion}.zip -r webapp/dist"
                    sh "curl -v -u admin:password --upload-file webapp/lms-${packageJSONVersion}.zip http://35.86.122.209:8081/repository/lms/"
                }
            }
        }
        stage('Deploy LMS') {
           steps {
               script {
                   def packageJson = readJSON file: 'webapp/package.json'
                   def packageJSONVersion = packageJson.version
                   echo "${packageJSONVersion}"
                   sh "curl -u admin:password -X GET \'http://35.86.122.209:8081/repository/lms/lms-${packageJSONVersion}.zip\' --output lms-'${packageJSONVersion}'.zip"
                   sh 'sudo rm -rf /var/www/html/*'
                   sh "sudo unzip -o lms-'${packageJSONVersion}'.zip"
                   sh "sudo cp -r webapp/dist/* /var/www/html"
               }
           }
       }
        stage('Clean Up') {
            steps {
                cleanWs()
            }
        }
    }
}