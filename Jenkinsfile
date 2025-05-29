pipeline {
    agent any

    environment {
        SONAR_TOKEN = credentials('SONAR_TOKEN')
        JAVA_OPTS = "-Dorg.jenkinsci.plugins.durabletask.BourneShellScript.HEARTBEAT_CHECK_INTERVAL=3600"
    }

    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

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
                    withEnv(["SONAR_BRANCH=${branchName}"]) {
                        sh '''
                            export JAVA_OPTS="${JAVA_OPTS}"
                            export SONAR_TOKEN="${SONAR_TOKEN}"
                            mvn sonar:sonar \
                                -Dsonar.token=$SONAR_TOKEN \
                                -Dsonar.projectKey=Gimena-ccoracc_PSW \
                                -Dsonar.branch.name=$SONAR_BRANCH
                        '''
                    }
                }
            }
        }

        stage('Package') {
            steps {
                sh '''
                    echo "Buscando el JAR generado..."
                    ls -lh target/*.jar

                    echo "Copiando el JAR generado a workspace raíz (opcional)..."
                    cp target/*.jar ./ || true
                '''
            }
        }
    }

    post {
        success {
            echo "Pipeline ejecutado correctamente."
        }
        failure {
            echo "El pipeline ha fallado."
        }
    }
}
