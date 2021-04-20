pipeline {
  
  agent none
 //// 
  stages {
    // debut Compilation et tests
    stage('Compilation et tests') {
       agent {
          label 'agent_java'
       }
      // debut stages
       stages {
         // debut teste 
        stage('Teste') {
            steps {
              sh 'mvn clean test'
            }
         } // Fin stage teste
         //  Debut Compilation
         stage('Compilation') {
            steps {  
              sh 'mvn -B -DskipTests clean package'
            } // Fin compilation
         }// debut Pub
         stage('Publication du binaire') {

          steps {
           // sh "curl -u admin:nexus --upload-file target/*.war 'http://84.39.43.46:8081/repository/depot_test/test${BUILD_NUMBER}.war'"   
            sh "curl -u admin:nexus --upload-file /home/jenkins/workspace/cker_pipeline_multibranch_master/target/*.war 'http://10.10.20.31:8081/repository/depot_test/hello-${BUILD_NUMBER}.war'"
          }
         }// fin pub
       }  // Fin stages
     } // Fin compi et tests
      // Debut test deployement
     stage ('Tests de deployement') {
       agent {
         label 'agent_tomcat'
       }
       stages {
         stage ('téléchargement  du binaire') {
            steps {
                sh "wget -P /home/jenkins/tomcat/webapps http://10.10.20.31:8081/repository/depot_test/hello-${BUILD_NUMBER}.war"
                sh "mv /home/jenkins/tomcat/webapps/hello-${BUILD_NUMBER}.war /home/jenkins/tomcat/webapps/app.war"
            }
          }
          stage ('test de performance') {
           steps {
              sh '/home/jenkins/apache-jmeter/bin/jmeter.sh /home/jenkins/test_report.jtl'
           }
          } 
            stage ('Validation de l\'application') {
              steps {
                sh "curl -u admin:nexus --upload-file /home/jenkins/tomcat/webapps/app.war 'http://10.10.20.31:8081/repository/hello_fiable/app_fiable${BUILD_NUMBER}.war'"   
              }
            }
         } // Fin stages
     } // fin test delpoyement agent_tomcat
         ///////////////////////////
       stage ('Création de l\'image') {
         agent {
           label 'agent docker' 
         }
         stages {
           stage ('Téléchargement du binaire') {
             steps {
               sh "wget -P /home/jenkins/docker/tomcat_app http://10.10.20.31:8081/repository/hello_fiable/app_fiable${BUILD_NUMBER}.war"
               sh 'mv /home/jenkins/docker/tomcat_app/app_fiable${BUILD_NUMBER}.war /home/jenkins/docker/tomcat_app/app.war'
             }
           }
           stage ('Compilation de l\'image') {
             steps {
              sh 'docker build -t tomcat_app /home/jenkins/docker/tomcat_app' 
             }
           }
           stage ('Stockage del\'image') {
             steps {
                sh "docker tag tomcat_app/sophiegillot/tomcat_app:${BUILD_NUMBER}"
                sh 'docker tag tomcat_app sophiegillot/tomcat_app'
                sh "docker login -u sophiegillot -p 'Reine?1965’"
                sh "docker push sophiegillot/tomcat_app:${BUILD_NUMBER}"
                sh 'docker push sophiegillot/tomcat_app'
             }
           }
         } //fin stages
       }// fin stage  docker
    }
 }
