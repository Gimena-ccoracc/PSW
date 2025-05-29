pipeline {
    agent any

    environment {
        SONAR_TOKEN = credentials('SONAR_TOKEN') // Asegúrate que esta credencial esté correctamente definida
    }

    stages {
        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('SonarCloud Analysis') {
            steps {
                script {
                    def branchName = env.BRANCH_NAME ?: 'workshop'
                    // Usamos una llamada segura sin interpolación
                    withEnv(["SONAR_TOKEN=${SONAR_TOKEN}"]) {
                        sh '''
                            echo "Iniciando análisis con SonarCloud..."
                            mvn sonar:sonar \
                                -Dsonar.token=$SONAR_TOKEN \
                                -Dsonar.projectKey=Gimena-ccoracc_PSW \
                                -Dsonar.branch.name=''' + branchName + ''' || true
                        '''
                    }
                }
            }
        }

        stage('Package') {
            steps {
                script {
                    sh '''
                        echo "Buscando el JAR generado..."
                        ls -lh target/*.jar

                        echo "Copiando el JAR generado a workspace raíz (opcional)..."
                        cp target/*.jar ./ || true
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline ejecutado correctamente."
        }
        failure {
            echo "❌ El pipeline ha fallado."
        }
    }
}
