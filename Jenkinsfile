pipeline {
    agent any
    options {
        skipStagesAfterUnstable()
    }
    stages {
        stage('Prepare') {
            steps {
                // Criar um ambiente virtual isolado no workspace
                sh 'python3 -m venv venv'

                // Ativar o venv e instalar pacotes necessários
                sh '. venv/bin/activate && pip install --upgrade pip pytest pyinstaller'

                // Criar pastas necessárias e limpar builds antigos
                sh 'mkdir -p test-reports'
                sh 'rm -rf dist/* build/* __pycache__'
            }
        }
        stage('Build') {
            steps {
                // Compilar os scripts Python dentro do venv
                sh '. venv/bin/activate && python -m py_compile sources/add2vals.py sources/calc.py'
            }
        }
        stage('Test') {
            steps {
                // Rodar os testes com pytest dentro do venv
                sh '. venv/bin/activate && pytest --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }
        stage('Deliver') {
            steps {
                // Gerar executável com PyInstaller dentro do venv
                sh '. venv/bin/activate && pyinstaller --onefile sources/add2vals.py'
            }
            post {
                success {
                    archiveArtifacts 'dist/add2vals'
                }
            }
        }
    }
}