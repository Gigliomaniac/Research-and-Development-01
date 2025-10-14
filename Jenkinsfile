pipeline
{
    agent any
    environment
    {
        // Docker Hub credentials ID stored in Jenkins
        DOCKERHUB_CREDENTIALS ='dockerconnect'
        IMAGE_NAME ='gigliomaniac1/Research-and-Development-01'
    }

    stages
    {
// Clone from Github with scm
        stage('Cloning Git')
        {
            steps
            {
                checkout scm
            }
        }
// Scan the things
        stage('SAST')
        {
            steps
            {
                sh 'echo Running SAST scan with snyk'
            }
        }

        stage('BUILD-and-TAG')
        {
            agent { label 'Miquella'}

            steps 
            {
                script
                {
                    // Build Docker image using Jenkins Docker Pipeline API
                    echo "Build Docker image ${IMAGE_NAME}..."
                    app = docker.build("${IMAGE_NAME}")
                    app.tag("latest")

                }
            }
        }

        stage('POST-TO-DOCKERHUB')
        {
            agent { label 'Miquella'}

            steps 
            {
                script
                {
                    echo "Pushing image ${IMAGE_NAME}: latest to Docker Hub..."
                    docker.withRegistry('https://registry.hub.docker.com',"${DOCKERHUB_CREDENTIALS}" )
                    {
                        app.push("latest")
                    }
                    
                }
            }
        }

        stage('DAST')
        {
            steps
            {
                sh 'echo Running DAST scan...'
            }
        }

        stage('DEPLOYMENT')
        {
            agent { label 'Miquella'}

            steps 
            {
                echo 'Starting deployment using docker-compose...'
                script
                {
                    dir("${WORKSPACE}")
                    {
                        sh '''
                            docker-compose down
                            docker-compose up -d
                            docker ps
                        '''
                    }
                    
                }
                echo 'Deployment completed successfully!'
            }
        }
    }
}
