def clonePythonRepo() {
    dir('python-greetings') {
        deleteDir()
        git branch: 'main', url: 'https://github.com/mtararujs/python-greetings.git'
    }
}

def cloneTestsRepo() {
    dir('course-js-api-framework') {
        deleteDir()
        git branch: 'main', url: 'https://github.com/mtararujs/course-js-api-framework.git'
    }
}

def installPythonDeps() {
    dir('python-greetings') {
        sh 'echo "Creating Python virtual environment..."'
        sh 'python3 -m venv venv'

        sh 'echo "Installing Python dependencies..."'
        sh './venv/bin/python -m pip install -r requirements.txt'
    }
}

def deployEnv(String envName, String port) {
    echo "Deploying to ${envName} environment on port ${port}..."

    clonePythonRepo()
    installPythonDeps()

    dir('python-greetings') {
        sh "echo 'Stopping existing service greetings-app-${envName} if it exists...'"
        sh "pm2 delete greetings-app-${envName} || true"

        sh "echo 'Starting greetings-app-${envName} with PM2...'"
        sh "pm2 start app.py --name greetings-app-${envName} --interpreter ./venv/bin/python -- --port ${port}"

        sh "echo 'Checking API on ${envName}...'"
        sh "curl -f http://127.0.0.1:${port}/greetings"

        sh "echo 'PM2 process list:'"
        sh 'pm2 list'
    }
}

def testEnv(String envName) {
    echo "Running API tests on ${envName} environment..."

    cloneTestsRepo()

    dir('course-js-api-framework') {
        sh 'echo "Installing npm dependencies..."'
        sh 'npm install'

        sh "echo 'Running greetings tests for ${envName}...'"
        sh "npm run greetings greetings_${envName}"
    }
}

pipeline {
    agent any

    environment {
        PATH = "/opt/homebrew/bin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:${env.PATH}"
    }

    stages {
        stage('install-pip-deps') {
            steps {
                echo 'Installing all required dependencies for Python microservice...'
                script {
                    clonePythonRepo()
                    dir('python-greetings') {
                        sh 'echo "Listing repository files..."'
                        sh 'ls -la'
                    }
                    installPythonDeps()
                }
            }
        }

        stage('deploy-to-dev') {
            steps {
                echo 'Deploying application to DEV...'
                script {
                    deployEnv('dev', '7001')
                }
            }
        }

        stage('tests-on-dev') {
            steps {
                echo 'Running tests on DEV...'
                script {
                    testEnv('dev')
                }
            }
        }

        stage('deploy-to-stg') {
            steps {
                echo 'Deploying application to STG...'
                script {
                    deployEnv('stg', '7002')
                }
            }
        }

        stage('tests-on-stg') {
            steps {
                echo 'Running tests on STG...'
                script {
                    testEnv('stg')
                }
            }
        }

        stage('deploy-to-preprod') {
            steps {
                echo 'Deploying application to PREPROD...'
                script {
                    deployEnv('preprod', '7003')
                }
            }
        }

        stage('tests-on-preprod') {
            steps {
                echo 'Running tests on PREPROD...'
                script {
                    testEnv('preprod')
                }
            }
        }

        stage('deploy-to-prod') {
            steps {
                echo 'Deploying application to PROD...'
                script {
                    deployEnv('prod', '7004')
                }
            }
        }

        stage('tests-on-prod') {
            steps {
                echo 'Running tests on PROD...'
                script {
                    testEnv('prod')
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution finished.'
        }
        success {
            echo 'All deployments and tests completed successfully.'
        }
        failure {
            echo 'Pipeline failed. Check the logs above.'
        }
    }
}
