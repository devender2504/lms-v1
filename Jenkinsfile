pipeline {
   agent any
 stages {
       stage('Code Quality') {
           steps {
               echo 'Sonar Analysis Started'
               sh 'cd webapp && sudo docker run  --rm -e SONAR_HOST_URL="http://15.206.163.101:9000" -v ".:/usr/src" sonarsource/sonar-scanner-cli -Dsonar.token=sqp_9a727733e83f6288f2cee3d60ff77eaeff9b5b51 -Dsonar.projectKey=lms'
               echo 'Sonar Analysis Completed'
           }
       }

       stage('Build LMS') {
           steps {
               echo 'LMS Build Started'
               sh 'cd webapp && npm install && npm run build'
               echo 'LMS Build Completed'
           }
       }

       stage('Publish LMS') {
           steps {
               script {
                   def packageJson = readJSON file: 'webapp/package.json'
                   def packageJSONVersion = packageJson.version
                   echo "${packageJSONVersion}"
                   sh "zip webapp/lms-${packageJSONVersion}.zip -r webapp/dist"
                   sh "curl -v -u admin:12345678 --upload-file webapp/lms-${packageJSONVersion}.zip http://15.206.163.101:8081/repository/lms/"
               }
           }
       }

       stage('Deploy LMS') {
           steps {
               script {
                   def packageJson = readJSON file: 'webapp/package.json'
                   def packageJSONVersion = packageJson.version
                   echo "${packageJSONVersion}"
                   sh "curl -u admin:lms12345 -X GET \'http://35.90.115.123:8081/repository/lms/lms-${packageJSONVersion}.zip\' --output lms-'${packageJSONVersion}'.zip"
                   sh 'sudo rm -rf /var/www/html/*'
                   sh "sudo unzip -o lms-'${packageJSONVersion}'.zip"
                   sh "sudo cp -r webapp/dist/* /var/www/html"
               }
           }
       }
       stage('Clean Up Workspace') {
           steps {
                   echo 'Cleaning Work Space'
                   // Install Cleanup Workspace plugin to make below command work
                   cleanWs()
           }
       }
   }
}
