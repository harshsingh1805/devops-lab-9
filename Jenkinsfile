pipeline {
    agent any

    stages {

        stage('Build') {
            steps {
                echo "Installing dependencies..."

                sh '''
                    if [ -f requirements.txt ]; then
                        echo "Installing from requirements.txt..."
                        pip3 install --user -r requirements.txt
                    else
                        echo "No requirements.txt found, skipping."
                    fi
                '''
            }
        }

        stage('Test') {
            steps {
                echo "Running tests..."
                sh '''
                    if [ -f tests/test_app.py ]; then
                        python3 -m unittest tests/test_app.py
                    else
                        echo "No tests found, skipping."
                    fi
                '''
            }
        }

        stage('Run Application') {
            steps {
                echo "Starting application..."
                sh '''
                    python3 app.py &
                    sleep 5
                '''
            }
        }

        stage('Test Application') {
            steps {
                echo "Testing running application..."
                sh '''
                    curl -I http://localhost:5000 || echo "App did not start"
                '''
            }
        }
    }

    post {
        failure {
            echo "Pipeline failed!"
        }
        success {
            echo "Pipeline succeeded!"
        }
    }
}
