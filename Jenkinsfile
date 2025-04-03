pipeline {
    agent any
    environment {
        // You can add additional environment variables if needed.
        PYTHON_VERSION = "3.13"  // Informational only, since 'python' should be Python 3.13.
    }
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
                sh '''
                python -m pip install --upgrade pip
                pip install -r requirements.txt
                pip install dvc[all] mlflow
                '''
            }
        }
        stage('Pull DVC Data') {
            steps {
                echo "Pulling DVC data..."
                sh "dvc pull"
            }
        }
        stage('Data Cleaning') {
            steps {
                echo "Running data cleaning stage..."
                // This replicates your GitHub Actions command for data cleaning.
                sh '''
                dvc run -n data_cleaning -d src/ml/data_cleaning.py -d data/raw/comments.csv -o data/processed/cleaned_comments.csv python src/ml/data_cleaning.py
                '''
            }
        }
        stage('Feature Engineering') {
            steps {
                echo "Running feature engineering stage..."
                sh '''
                dvc run -n feature_engineering -d src/ml/feature_engineering.py -d data/processed/cleaned_comments.csv -o data/processed/features.csv -o data/processed/sentence_embeddings.pt python src/ml/feature_engineering.py
                '''
            }
        }
        stage('Model Training') {
            steps {
                echo "Running model training stage..."
                sh '''
                dvc run -n model_training -d src/ml/train.py -d data/processed/features.csv -d data/processed/sentence_embeddings.pt -o models/xg_model.pkl -o models/label_encoder.pkl -o mlruns/ python src/ml/train.py
                '''
            }
        }
        stage('Promote Model') {
            steps {
                echo "Running model promotion stage..."
                sh '''
                dvc run -n promote_model -d models/xg_model.pkl -d models/label_encoder.pkl -d mlruns/ -o models/YouTube_Comment_Sentiment_Analysis/ python src/ml/promote_model.py
                '''
            }
        }
        stage('Commit & Push Changes') {
            steps {
                echo "Committing and pushing updated DVC files..."
                sh '''
                git add dvc.yaml dvc.lock
                git commit -m "Update DVC pipeline" || echo "No changes to commit"
                git push origin main
                '''
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
