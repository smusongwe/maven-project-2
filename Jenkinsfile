pipeline {
  agent any
  environment {
    WORKSPACE = "${env.WORKSPACE}"
  }
  tools {
    maven 'localMaven'
    jdk 'localJdk'
  }
  stages {
    stage('Build') {
      steps {
        sh 'mvn clean package'
      }
      post {
        success {
          echo ' now Archiving '
          archiveArtifacts artifacts: '**/*.war'
        }
      }
    }
    stage('SonarQube Scan') {
      steps {
        sh """mvn sonar:sonar \
              -Dsonar.host.url=http://52.32.237.65:9000 \
              -Dsonar.login=d36b776f4d4e0f274520ca522960263d99ae3938"""
      }
    }
    stage('Nexus Upload') {
      steps {
        nexusArtifactUploader(
          nexusVersion: 'nexus3',
          protocol: 'http',
          nexusUrl: 'ec2-34-219-128-244.us-west-2.compute.amazonaws.com:8081',
          groupId: 'MyWebApp',
          version: '1.0-SNAPSHOT',
          repository: 'maven-snapshots',
          credentialsId: '1c6d2590-a01d-4ba1-a516-ac128254d645',
          artifacts: [
            [artifactId: "MyWebApp",
            classifier: '',
            file: 'MyWebApp/target/MyWebApp.war',
            type: 'war'
            ]
          ]
        )
      }
    }
    // stage('Upload to Artifactory') {
    //   steps {
    //     sh "mvn clean deploy -DskipTests"
    //   }

    // }
    stage('Dev Deploy') {
        steps {
          echo "Deploying to Dev"
          deploy adapters: [tomcat9(credentialsId: 'ade63b67-a796-407e-9bb8-a33f79ea9357', path: '', 
          url: 'http://ec2-52-43-59-198.us-west-2.compute.amazonaws.com:8080')], 
          contextPath: null, war: '**/*.war'

        }
    }
    // stage('Deploy to DEV') {
    //   environment {
    //     HOSTS = "dev"
    //   }
    //   steps {
    //     sh "ansible-playbook ${WORKSPACE}/deploy.yaml --extra-vars \"hosts=$HOSTS workspace_path=$WORKSPACE\""
    //   }

    // }
    // stage('Approval') {
    //   steps {
    //     input('Do you want to proceed?')
    //   }
    // }
  //   stage('Deploy to PROD') {
  //     environment {
  //       HOSTS = "prod"
  //     }
  //     steps {
  //       sh "ansible-playbook ${WORKSPACE}/deploy.yaml --extra-vars \"hosts=$HOSTS workspace_path=$WORKSPACE\""
  //     }
  //   }
  // }

}
}
