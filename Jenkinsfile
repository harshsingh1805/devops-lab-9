pipeline {
    agent any

    stages {

        stage('Build') {
            steps {
                echo "Installing dependencies..."

                sh '''
                    # Create venv if not exists
                    if [ ! -d "venv" ]; then
                        python3 -m venv venv
                    fi

                    # Activate venv
                    . venv/bin/activate

                    if [ -f requirements.txt ]; then
                        echo "Installing from requirements.txt..."
                        pip install --break-system-packages -r requirements.txt
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
                    . venv/bin/activate
                    if [ -f tests/test_app.py ]; then
                        python -m unittest tests/test_app.py
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
                    . venv/bin/activate
                    python app.py &
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
