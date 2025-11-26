pipeline {
    agent any

    stages {

        stage('Build') {
            steps {
                echo 'Installing dependencies...'

                sh '''
                docker run --rm \
                    -v $WORKSPACE:/app \
                    -w /app \
                    python:3.9 bash -c "
                        if [ -f requirements.txt ]; then
                            echo 'Installing from requirements.txt...'
                            pip install -r requirements.txt
                        else
                            echo 'No requirements.txt found, skipping.'
                        fi
                    "
                '''
            }
        }

        stage('Test') {
            steps {
                echo 'Running unit tests inside Python Docker container...'

                sh '''
                docker run --rm \
                    -v $WORKSPACE:/app \
                    -w /app \
                    python:3.9 python -m unittest discover -s .
                '''
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application...'

                sh '''
                mkdir -p $WORKSPACE/python-app-deploy
                cp $WORKSPACE/app.py $WORKSPACE/python-app-deploy/
                '''
            }
        }

        stage('Run Application') {
            steps {
                echo 'Running application inside Docker...'

                sh '''
                docker rm -f python_app_container || true

                docker run -d --name python_app_container \
                    -v $WORKSPACE/python-app-deploy:/app \
                    -w /app \
                    python:3.9 python app.py
                '''
            }
        }

        stage('Test Application') {
            steps {
                echo 'Testing running application...'

                sh '''
                docker run --rm \
                    -v $WORKSPACE:/app \
                    -w /app \
                    python:3.9 python test_app.py
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
