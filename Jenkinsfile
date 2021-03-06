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
              image 'selenium/standalone-chrome'
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
    stage('QA') {
      agent {
        docker {
          args '-p 11080:8080'
          image 'tomcat:8.0'
        }

      }
      steps {
        unstash 'server'
        unstash 'client'
        sh '''APP_DIR=/usr/local/tomcat/webapps
rm -rf $APP_DIR/ROOT
cp target/server.war $APP_DIR/server.war
mkdir -p $APP_DIR/ROOT
cp dist/* $APP_DIR/ROOT
/usr/local/tomcat/bin/startup.sh'''
      }
    }
  }
}