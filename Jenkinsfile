pipeline {
    agent any


    environment {
        COMPOSE_PROJECT_NAME = 'pmng'
        COMPOSE_FILE = 'docker-compose.yml'
        ENV_SERVER_PORT = '8080'
        ENV_DB_HOST = 'host.docker.internal'
        ENV_DB_PORT = '3306'
        ENV_DB_NAME = 'people-dev'
        ENV_DB_USER = 'root'
        ENV_DB_PASS = 'root'
    }


    stages {
        stage('Parar servicios'){
            steps{
                echo 'Deteniendo contenedores..'
                sh '''
                    docker compose -p ${COMPOSE_PROJECT_NAME} -f ${COMPOSE_FILE} down --remove-orphans || true
                    docker rm -f pmng-servicios pmng-cliente || true
                '''
            }
        }


        stage('Eliminar imagenes'){
            steps{
                echo 'Eliminando imagenes antiguas..'
                sh '''
                    IMAGES=$(docker compose -p ${COMPOSE_PROJECT_NAME} -f ${COMPOSE_FILE} config --images | sort -u)


                    for IMAGE in $IMAGES; do
                        echo "Eliminando imagen: $IMAGE"
                        docker rmi "$IMAGE" || true
                    done
                '''
            }
        }


        stage('Construir imagenes'){
            steps{
                echo 'Generando nuevas imagenes...'
                sh '''
                    docker compose -p ${COMPOSE_PROJECT_NAME} -f ${COMPOSE_FILE} build --no-cache
                '''
            }
        }


        stage('Levantar servicios'){
            steps{
                echo 'Generando contenedores..'
                sh '''
                    docker compose -p ${COMPOSE_PROJECT_NAME} -f ${COMPOSE_FILE} up -d --force-recreate
                '''
            }
        }
    }


    post {
        success {
            echo 'Construccion del proyecto finalizada'
        }


        failure {
            echo 'Ocurrio un erroe en la construcción del proyecto'
        }
    }


}
