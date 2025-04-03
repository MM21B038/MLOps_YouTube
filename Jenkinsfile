pipeline {
    agent any
    stages {
        stage('Checkout Code') {
            steps {
                echo "Checking out the repository..."
                checkout scm
            }
        }
        stage('Set Up Python Environment') {
            steps {
                echo "Setting up Python environment..."
                bat """
                python -m pip install --upgrade pip
                pip install -r requirements.txt
                pip install dvc mlflow
                """
            }
        }
        stage('Pull DVC Data') {
            steps {
                echo "Configuring DVC remote and pulling data..."
                bat """
                dvc remote add -d local_storage E:/manav/dvc-storage || echo DVC remote already set
                dvc pull
                """
            }
        }
        stage('Data Ingestion') {
            steps {
                echo "Running data ingestion stage..."
                bat "dvc repro data_ingestion"
            }
        }
        stage('Data Cleaning') {
            steps {
                echo "Running data cleaning stage..."
                bat "dvc repro data_cleaning"
            }
        }
        stage('Feature Engineering') {
            steps {
                echo "Running feature engineering stage..."
                bat "dvc repro feature_engineering"
            }
        }
        stage('Model Training') {
            steps {
                echo "Running model training stage..."
                bat "dvc repro train"
            }
        }
        stage('Promote Model') {
            steps {
                echo "Running model promotion stage..."
                bat "dvc repro promote_model"
            }
        }
        stage('Commit & Push Changes') {
            steps {
                echo "Committing and pushing updated DVC files..."
                bat """
                git config --global user.email "your-email@example.com"
                git config --global user.name "Jenkins CI"
                git add dvc.yaml dvc.lock
                git commit -m "Update DVC pipeline" || echo No changes to commit
                git push origin main
                """
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
