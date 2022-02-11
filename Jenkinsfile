pipeline {
agent any
tools{
maven 'MAVEN11'
jdk 'JAVA11'
}



stages {
stage('checkout') {
steps {
echo 'Hello World'
git url: 'https://github.com/Mohit722/spring-mvc-crud.git', branch: 'master'
}
}
stage('Build'){
steps{
bat 'mvn clean install'
}
}
stage('SonarQube Ananlysis'){
steps{
withSonarQubeEnv('sonarqube2'){
bat 'mvn sonar:sonar'
}
}
}
stage ('Deploy war to tomcat') {
steps {
deploy adapters: [tomcat9(credentialsId: 'tomcat1', path: '', url: 'http://localhost:9090')], contextPath: 'spring4', onFailure: false, war: 'target/*.war'
//deploy adapters: [tomcat8(credentialsId: 'demo', path: '', url: 'http://localhost:7070')], contextPath: 'spring4', onFailure: false, war: 'target/*.war'
}
}
}
}

