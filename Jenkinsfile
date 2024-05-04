pipeline {
    agent any

    environment {
        DOTNET_CLI_HOME = "C:\\Program Files\\dotnet"
    }


    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                script {
                    // Restoring dependencies
                    //bat "cd ${DOTNET_CLI_HOME} && dotnet restore"
                    if(isUnix()){
                        //sh "echo 'P@ssw0rd@123456789' | sudo -u hadi -S chmod -R 777 ./"
                        sh "echo Docker@123 | sudo -S dotnet restore"
                    }else
                        bat "dotnet restore"

                    // Building the application
                    if(isUnix())
                        sh "dotnet build --configuration Release"
                    else 
                        bat "dotnet build --configuration Release"
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    if(isUnix())
                        sh "dotnet test --no-restore --configuration Release"
                    else 
                        // Running tests
                        bat "dotnet test --no-restore --configuration Release"
                }
            }
        }
        
        stage('build Docker') {
            steps {
                script {
                    if(isUnix())
                        sh "docker build -t test-web-application-docker-published ./TestWebApplication"
                    else 
                        // make  sure we have a tag to use for docker image. If not, then fail the job
                        bat "docker build -t test-web-application-docker-published ./TestWebApplication"

                }
            }
        }

        stage('update container') {
            steps {
                script {
                    if(isUnix())
                        sh "docker rm -f test-web-app-container"
                    else 
                        // remove the container
                        bat "docker rm -f test-web-app-container"
                    
                    if(isUnix())
                        sh "docker run --name test-web-app-container -d -p 8798:80 test-web-application-docker-published"
                    else 
                        // run container from saved image and check if it is running
                        bat "docker run --name test-web-app-container -d -p 8798:80 test-web-application-docker-published"
                    
                    // this is empty line.
                    // save docker image aas tar file
                    //bat "docker save -o test-web-application-docker-published.tar test-web-application-docker-published"

                }
            }
        }

        stage('Publish') {
            steps {
                script {
                    if(isUnix())
                        sh "dotnet publish --no-restore --configuration Release --output .\\publish"
                    else 
                        // Publishing the application
                        bat "dotnet publish --no-restore --configuration Release --output .\\publish"
                }
            }
        }
    }

    post {
        success {
            echo 'Build, test, and publish successful!'
        }
    }
}
