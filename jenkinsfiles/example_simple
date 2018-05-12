#!/bin/env groovy

pipeline {
  agent none
  tools { 
     maven 'maven3.5.3' 
    }
  environment {
    IMAGE = "liatrio/petclinic-tomcat"
  }

  stages {
    stage('Compiling') {
      agent any
      steps {
        sh 'mvn clean compile'
	//sh 'mvn clean findbugs:findbugs package'
      }
    }
    stage('Testing') {
      agent any
      steps {
        sh 'mvn test'
	//sh 'mvn clean findbugs:findbugs package'
      }
    }
    stage('Building') {
      agent any
      steps {
        sh 'mvn package -Dmaven.test.skip=true'
	//sh 'mvn clean findbugs:findbugs package'
      }
    }
    stage('Sonar Scan') {
      agent any
      steps {
         //sh 'mvn sonar:sonar -Dsonar.host.url=http://10.11.168.137:9000  -Dsonar.login=6a692f33b27cfc07db446bdd45ee3f683e84eef5'
         sh 'mvn sonar:sonar -Dsonar.host.url=http://192.168.3.29:9000  -Dsonar.login=6a692f33b27cfc07db446bdd45ee3f683e84eef5'
         //sh 'echo skip the Sonar Scan'
      }
    }
    stage('Build container') {
      agent any
      steps {
        script {
          if ( env.BRANCH_NAME == 'master' ) {
            pom = readMavenPom file: 'pom.xml'
            TAG = pom.version
          } else {
            TAG = env.BRANCH_NAME
          }
          sh "docker build -t ${env.IMAGE}:${TAG} ."
        }
      }
    }
    stage('Deploy to Dev') {
      agent any
      steps {
        sh 'docker rm -f petclinic-tomcat-temp || true'
        sh "docker run -d -p 9966:8080 --name petclinic-tomcat-temp ${env.IMAGE}:${TAG}"
      }
    }
  }
}
