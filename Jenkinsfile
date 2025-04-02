pipeline {
    agent any

    environment {
        PYTHON_ENV = "python3"
        DVC_STORAGE_PATH = "E:/manav/dvc-storage"
    }

    stages {
        stage('Checkout Code') {
            steps {
                script {
                    echo "Checking out the repository..."
                    checkout scm
                }
            }
        }

        stage('Set Up Environment') {
            steps {
                script {
                    echo "Setting up Python environment..."
                    sh '''
                    python3 -m venv venv
                    source venv/bin/activate
                    pip install --upgrade pip
                    pip install -r requirements.txt
                    pip install dvc mlflow
                    '''
                }
            }
        }

        stage('Pull DVC Data') {
            steps {
                script {
                    echo "Pulling data from local DVC storage..."
                    sh '''
                    source venv/bin/activate
                    dvc remote add -d local_storage ${DVC_STORAGE_PATH} || echo "DVC storage already set"
                    dvc pull
                    '''
                }
            }
        }

        stage('Data Cleaning') {
            steps {
                script {
                    echo "Running data cleaning..."
                    sh '''
                    source venv/bin/activate
                    dvc repro data_cleaning
                    '''
                }
            }
        }

        stage('Feature Engineering') {
            steps {
                script {
                    echo "Running feature engineering..."
                    sh '''
                    source venv/bin/activate
                    dvc repro feature_engineering
                    '''
                }
            }
        }

        stage('Model Training') {
            steps {
                script {
                    echo "Training the model..."
                    sh '''
                    source venv/bin/activate
                    dvc repro model_training
                    '''
                }
            }
        }

        stage('Promote Model') {
            steps {
                script {
                    echo "Promoting the model..."
                    sh '''
                    source venv/bin/activate
                    dvc repro promote_model
                    '''
                }
            }
        }

        stage('Commit & Push Changes') {
            steps {
                script {
                    echo "Committing updated DVC files..."
                    sh '''
                    git config --global user.email "your-email@example.com"
                    git config --global user.name "Jenkins CI"
                    git add dvc.yaml dvc.lock
                    git commit -m "Update DVC pipeline"
                    git push origin main
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully! ✅"
        }
        failure {
            echo "Pipeline failed ❌. Check logs for details."
        }
    }
}
