pipeline {
    agent any

    stages {
        stage('install-pip-deps') {
            steps {
                echo "Installing dependencies for python-greetings.."
                script {
                    installPipDeps()
                }
            }
        }

        stage('deploy-to-dev') {
            steps {
                script {
                    deployToEnvironment('dev', 7001)
                }
            }
        }

        stage('tests-on-dev') {
            steps {
                script {
                    runApiTests('dev')
                }
            }
        }

        stage('deploy-to-stg') {
            steps {
                script {
                    deployToEnvironment('stg', 7002)
                }
            }
        }

        stage('tests-on-stg') {
            steps {
                script {
                    runApiTests('stg')
                }
            }
        }

        stage('deploy-to-preprod') {
            steps {
                script {
                    deployToEnvironment('preprod', 7003)
                }
            }
        }

        stage('tests-on-preprod') {
            steps {
                script {
                    runApiTests('preprod')
                }
            }
        }

        stage('deploy-to-prod') {
            steps {
                script {
                    deployToEnvironment('prod', 7004)
                }
            }
        }

        stage('tests-on-prod') {
            steps {
                script {
                    runApiTests('prod')
                }
            }
        }
    }
}

def installPipDeps() {
    echo "Cloning python-greetings repository.."
    git branch: 'main', poll: false, url: 'https://github.com/mtararujs/python-greetings.git'

    echo "Listing files in repository.."
    sh "ls"

    echo "Creating Python virtual environment.."
    sh "python3 -m venv venv"

    echo "Installing required Python packages..."
    sh "./venv/bin/python -m pip install -r requirements.txt"
}

def deployToEnvironment(String environmentName, int port) {

    echo "Deploying python-greetings to ${environmentName} environment.."

    installPipDeps()

    echo "Deleting old PM2 greetings-app-${environmentName}, if it exists.."
    sh "pm2 delete greetings-app-${environmentName} || exit 0"

    echo "Starting greetings-app-${environmentName} on port ${port}.."
    sh "pm2 start app.py --name greetings-app-${environmentName} --interpreter ./venv/bin/python -- --port ${port}"

    echo "PM2 process list after deployment to ${environmentName}:"
    sh "pm2 list"

}

def runApiTests(String environmentName) {
    echo "Running API tests on ${environmentName} environment.."
    echo "Cloning course-js-api-framework repository for ${environmentName} tests.."
    git branch: 'main', poll: false, url: 'https://github.com/mtararujs/course-js-api-framework.git'

    echo "Listing files in API test repository.."
    sh "ls"

    echo "Installing Node.js test dependencies.."
    sh "npm install"

    echo "Running greetings API tests for ${environmentName} environment.."
    sh "npm run greetings greetings_${environmentName}"
}