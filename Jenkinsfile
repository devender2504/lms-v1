
pipeline {
    agent any
    stages {
        stage('Code Quality') {
            steps {
                echo 'Building..'
                sh "cd webapp &&
                    sudo docker run  --rm -e SONAR_HOST_URL="http://15.207.113.66:9000" -v ".:/usr/src" sonarsource/sonar-scanner-cli -Dsonar.token=sqp_3de87fc07885a10d89d42d20587cdef7c1033929 -Dsonar.projectKey=lms"
                echo 'Code Quality Check Completed'
            }
        }
        stage('Build lms') {
            steps {
                echo 'Building lms..'
                sh "cd webapp && npm install && npm run build"
                echo 'Build Completed' 
            }
        }
        stage('Publish lms') {
            steps {
                echo 'Publishing lms....'
                script {
                    def packageJson = readJSON file: 'webapp/package.json'
                    def version = packageJson.version
                    echo "Current version: ${version}"
                    sh "zip webapp/lms-${version}.zip -r webapp/dist"
                    sh "curl -v -u admin:12345678 --upload-file webapp/lms-${version}.zip http://15.207.113.66:8081/repository/lms/"
                }
            }
        }
        stage('Deploy lms') {
            steps {
                echo 'Deploying lms....'
                script{
                    def packageJson = readJSON file: 'webapp/package.json'
                    def version = packageJson.version   
                    echo "Current version: ${version}"
                    sh "curl -u admin:12345678 -X GET 'http://15.207.113.66:8081/repository/lms/lms-${version}.zip' --output lms.zip"
                    sh "sudo rm -rf /var/www/html/*"
                    sh "sudo unzip -o lms-'${version}'.zip "
                    sh "sudo cp -r webapp/dist/* /var/www/html"

                }
            }
        }
        stage('Clean Up') {
            steps {
                echo 'Cleaning up...'
                cleanws()
            }
        }
    }
}
