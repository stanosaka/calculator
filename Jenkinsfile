pipeline {
     agent any
     stages {
          stage("Compile") {
               steps {
                    sh "./gradlew compileJava"
               }
          }
          stage("Unit test") {
               steps {
                    sh "./gradlew test"
               }
          }
          stage("Package") {
               steps {
                    sh "./gradlew build"
               }
          }

          stage("Docker build") {
               steps {
                    sh "docker build -t stanosaka/calculator ."
               }
          }

          stage("Docker login") {
               steps {
                    withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'docker-hub-credentials',
                               usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD']]) {
                         sh "docker login --username $USERNAME --password $PASSWORD"
                    }
               }
          }

          stage("Docker push") {
               steps {
                    sh "docker push stanosaka/calculator"
               }
          }

          stage("Deploy to staging") {
               steps {
                    sh "docker rm -f calculator"
                    sh "docker run -d --rm -p 8765:8080 --name calculator stanosaka/calculator"
               }
          }

          stage("Acceptance test") {
               steps {
                    sleep 60
                    sh "./acceptance-test.sh"
               }
          }
     }
}
