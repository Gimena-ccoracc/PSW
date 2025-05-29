pipeline {
    agent any

    environment {
        SONAR_TOKEN = credentials('SONAR_TOKEN')
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
            withEnv(["SONAR_TOKEN=${SONAR_TOKEN}", "BRANCH_NAME=${branchName}"]) {
                sh '''
                    mvn sonar:sonar \
                    -Dsonar.login=$SONAR_TOKEN \
                    -Dsonar.projectKey=Gimena-ccoracc_PSW \
                    -Dsonar.branch.name=$BRANCH_NAME
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
            echo "Pipeline ejecutado correctamente."
        }
        failure {
            echo "El pipeline ha fallado."
        }
    }
}
