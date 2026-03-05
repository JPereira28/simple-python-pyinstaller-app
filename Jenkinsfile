pipeline {
    agent any
    options {
        skipStagesAfterUnstable()
    }
    stages {
        stage('Prepare') {
            steps {
                // Instala PyInstaller e pytest localmente
                sh 'python3 -m pip install --user pyinstaller pytest'

                // Adiciona o PATH para que o Jenkins encontre os binários instalados em --user
                sh 'export PATH=$HOME/.local/bin:$PATH'

                // Cria pastas necessárias e limpa builds antigos
                sh 'mkdir -p test-reports'
                sh 'rm -rf dist/* build/* __pycache__'
            }
        }
        stage('Build') {
            steps {
                sh 'python3 -m py_compile sources/add2vals.py sources/calc.py'
            }
        }
        stage('Test') {
            steps {
                // Usa pytest em vez de py.test
                sh 'pytest --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }
        stage('Deliver') {
            steps {
                sh 'pyinstaller --onefile sources/add2vals.py'
            }
            post {
                success {
                    archiveArtifacts 'dist/add2vals'
                }
            }
        }
    }
}