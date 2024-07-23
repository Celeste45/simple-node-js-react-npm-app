// pipeline {
//     agent any
//     stages {
//         stage('Checkout SCM') {
//             steps {
//                 git 'https://github.com/Celeste45/simple-node-js-react-npm-app.git'
//             }
//         }
//         stage('Build') {
//             steps {
//                 sh 'npm install'
//             }
//         }
//         stage('Test') {
//             steps {
//                 sh './jenkins/scripts/test.sh'
//             }
//         }
//         stage('Deliver') { 
//             steps {
//                 sh './jenkins/scripts/deliver.sh' 
//                 input message: 'Finished using the web site? (Click "Proceed" to continue)' 
//                 sh './jenkins/scripts/kill.sh' 
//             }
//         }
//         stage('OWASP Dependency-Check Vulnerabilities') {
//             steps {
// 				dependencyCheck additionalArguments: '--format HTML --format XML --suppression suppression.xml', odcInstallation: 'OWASP Dependency-Check Vulnerabilities'
// 			}
//         }
//     }
//     post {
//         success {
//             dependencyCheckPublisher pattern: 'dependency-check-report.xml'
//         }
//     }
// }

pipeline {
  agent any
  stages {
    stage('Checkout') {
      steps {
        git branch: 'master', url: 'https://github.com/ScaleSec/vulnado.git'
      }
    }
    stage('Build') {
      steps {
        sh '/var/jenkins_home/apache-maven-3.6.3/bin/mvn --batch-mode -V -U -e clean verify -Dsurefire.useFile = false - Dmaven.test.failure.ignore'
      }
    }
    stage('Analysis') {
      steps {
        sh '/var/jenkins_home/apache-maven-3.6.3/bin/mvn --batch-mode -V -U -e checkstyle: checkstyle pmd: pmd pmd: cpd findbugs: findbugs'
      }
    }
  }
  post {
    always {
      junit testResults: '**/target/surefire-reports/TEST-*.xml'
      recordIssues enabledForFailure: true, tools: [mavenConsole(), java(), javaDoc()]
      recordIssues enabledForFailure: true, tool: checkStyle()
      recordIssues enabledForFailure: true, tool: spotBugs(pattern: '**/target/findbugsXml.xml')
      recordIssues enabledForFailure: true, tool: cpd(pattern: '**/target/cpd.xml')
      recordIssues enabledForFailure: true, tool: pmdParser(pattern: '**/target/pmd.xml')
    }
  }
}

// pipeline {
//   agent any
//   stages {
//     stage('Unit Test') {
//       steps {
//         sh 'mvn clean test'
//       }
//     }
//     stage('Deploy Standalone') {
//       steps {
//         sh 'mvn deploy -P standalone'
//       }
//     }
//     stage('Deploy AnyPoint') {
//       environment {
//         ANYPOINT_CREDENTIALS = credentials('anypoint.credentials')
//       }
//       steps {
//         sh 'mvn deploy -P arm -Darm.target.name=local-4.0.0-ee -Danypoint.username=${ANYPOINT_CREDENTIALS_USR}  -Danypoint.password=${ANYPOINT_CREDENTIALS_PSW}'
//       }
//     }
//     stage('Deploy CloudHub') {
//       environment {
//         ANYPOINT_CREDENTIALS = credentials('anypoint.credentials')
//       }
//       steps {
//         sh 'mvn deploy -P cloudhub -Dmule.version=4.0.0 -Danypoint.username=${ANYPOINT_CREDENTIALS_USR} -Danypoint.password=${ANYPOINT_CREDENTIALS_PSW}'
//       }
//     }
//   }
// }