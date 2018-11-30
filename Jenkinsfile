pipeline {
  agent any
  stages {
    stage('Build') {
      parallel {
        stage('Server') {
          agent {
            docker {
              image 'maven:3.5-jdk-8-slim'
            }

          }
          steps {
            sh '''echo "building server"
mvn -version
mkdir -p target
touch "target/server.war"
'''
            stash(name: 'server', includes: '**/*.war')
          }
        }
        stage('Client') {
          agent {
            docker {
              image 'node:6'
            }

          }
          steps {
            sh '''echo "build client code"
npm install --save react
mkdir -p dist
cat > dist/index.html <<EOF
Hello !
EOF
touch "dist/client.js"'''
            stash(name: 'client', includes: '**/dist/*')
          }
        }
      }
    }
    stage('Test') {
      parallel {
        stage('Chrome') {
          agent {
            docker {
              image 'standalone-chrome'
            }

          }
          steps {
            sh 'echo \'mvn test -Dbrowser=chrome\''
          }
        }
        stage('Firefox') {
          agent {
            docker {
              image 'selenium/standalone-firefox'
            }

          }
          steps {
            sh 'echo \'mvn test -Dbrowser=firefox\''
          }
        }
      }
    }
  }
}