//  jenkins pipeline to create webserver using data from s3 bucket

pipeline {
    agent { label 'Label-1' }

    environment {
        S3_BUCKET = 'academy.nv.batch3'
        S3_PATH = '2095_level/2095_level/'
        WEB_DIR = '/var/www/html'
    }
    stages {
        stage('Install Apache') {
            steps {
                script {
                    sh '''
                        echo "Updating packages and installing Apache..."
                        sudo apt-get update -y
                        sudo apt-get install apache2 -y
                        sudo systemctl enable apache2
                        sudo systemctl start apache2
                    '''
                }
            }
        }

        stage('Clear Old Content') {
            steps {
                script {
                    sh '''
                        echo "Clearing existing content in $WEB_DIR..."
                        sudo rm -rf $WEB_DIR/*
                    '''
                }
            }
        }

        stage('Install AWS CLi') {
            steps {
                script {
                    sh '''
                        echo "Installing AWS CLI if not present..."
                        if ! command -v aws &> /dev/null; then
                           sudo apt-get update
                           sudo apt-get install python3-pip -y
                           sudo pip install awscli --break-system-packages
                            aws --version
                        fi

                    '''
                }
            }
        }

        stage('Download Web Content from S3') {
            steps {
                script {
                    sh '''

                        echo "Copying web content from S3..."
                        sudo aws s3 cp s3://$S3_BUCKET/$S3_PATH $WEB_DIR/ --recursive
                    '''
                }
            }
        }

        stage('Set Permissions') {
            steps {
                script {
                    sh '''
                        echo "Setting permissions..."
                        sudo chown -R root:root $WEB_DIR
                        sudo chmod -R 755 $WEB_DIR
                    '''
                }
            }
        }

        stage('install tomcat') {
            steps {
                script {
                    sh '''
                        echo "installing tomcat application..."
                        sudo apt-get update
                        sudo apt-get install wget unzip -y 
                        wget https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.105/bin/apache-tomcat-9.0.105.zip
                        unzip ./*.zip
                        sudo sh ./apache-tomcat-9.0.105/bin/catalina.sh start
                        echo "Tomcat application is in working state..."
                    '''
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                script {
                    sh '''
                        echo "Deployment complete. Web server should be live at:"
                        hostname -I | awk '{print "http://" $1}'
                    '''
                }
            }
        }
    }
}
