pipeline {
    agent {
        docker {
            image 'python:3.9'
            args '-u'    // keeps output unbuffered
        }
    }

    stages {

        stage('Build') {
            steps {
                echo 'Installing dependencies...'
                
                // Install requirements if file exists
                sh '''
                if [ -f requirements.txt ]; then
                    pip install -r requirements.txt
                else
                    echo "No requirements.txt found, skipping pip install."
                fi
                '''
            }
        }

        stage('Test') {
            steps {
                echo 'Running unit tests...'
                sh 'python -m unittest discover -s .'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application...'

                sh '''
                mkdir -p ${WORKSPACE}/python-app-deploy
                cp ${WORKSPACE}/app.py ${WORKSPACE}/python-app-deploy/
                '''
            }
        }

        stage('Run Application') {
            steps {
                echo 'Starting application...'

                sh '''
                nohup python ${WORKSPACE}/python-app-deploy/app.py > ${WORKSPACE}/python-app-deploy/app.log 2>&1 &
                echo $! > ${WORKSPACE}/python-app-deploy/app.pid
                '''
            }
        }

        stage('Test Application') {
            steps {
                echo 'Testing running application...'

                sh 'python ${WORKSPACE}/test_app.py'
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check logs for details.'
        }
    }
}
