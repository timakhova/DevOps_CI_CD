# Домашнее задание к занятию "`Что такое DevOps. СI/СD`" - `Тимахова Наталья`

### Задание 1

1. Установила jenkins по оффиц. инструкции: https://github.com/timakhova/DevOps_CI_CD/blob/main/jenkins%20works.png
2. Открыла дженкинс в браузере: https://github.com/timakhova/DevOps_CI_CD/blob/main/jenkins%20LK.png
3. Установила гоуланг командами https://github.com/timakhova/DevOps_CI_CD/blob/main/go%20vstal.png
4. Создала Freestyle Project по инструкции в лекции, всё запустилось: https://github.com/timakhova/DevOps_CI_CD/blob/main/saksess-1.png, https://github.com/timakhova/DevOps_CI_CD/blob/main/saksess-2.png, https://github.com/timakhova/DevOps_CI_CD/blob/main/saksess-3.png

### Задание 2

Мой код:

```
pipeline {
 agent any
 stages {
  stage('Git') {
   steps {git 'https://github.com/netology-code/sdvps-materials.git'}
  }
  stage('Test') {
   steps {
    sh 'go test .'
   }
  }
  stage('Build') {
   steps {
    sh 'docker build . -t ubuntu-bionic:8082/hello-world:v$BUILD_NUMBER'
   }
  }
  stage('Push') {
   steps {
    sh 'docker login ubuntu-bionic:8082 -u admin -p admin && docker push ubuntu-bionic:8082/hello-world:v$BUILD_NUMBER && docker logout'   }
  }
 }
}

```

1. https://github.com/timakhova/DevOps_CI_CD/blob/main/2-1.png
2. https://github.com/timakhova/DevOps_CI_CD/blob/main/2-2.png
3. https://github.com/timakhova/DevOps_CI_CD/blob/main/2-3.png

### Задание 3

1. Установила нексус по инструкции с сайта https://timeweb.cloud/tutorials/cloud/ustanovka-i-ispolzovanie-nexus-repository-dlya-hraneniya-artefaktov https://github.com/timakhova/DevOps_CI_CD/blob/main/3-1%20nexus%20works.png
2. Зашла в акк: https://github.com/timakhova/DevOps_CI_CD/blob/main/3-2%20nexus%20web.png
3. Код для пайплайна:

```
pipeline {
    agent any

    environment {
        NEXUS_URL = 'http://158.160.160.168:8081'
        NEXUS_REPO = 'go-binaries'
        NEXUS_CREDENTIALS_ID = 'nexus-credentials'
        PATH = "/snap/bin:$PATH"
    }

    stages {
        stage('Git') {
            steps {
                git 'https://github.com/netology-code/sdvps-materials.git'
            }
        }
        stage('Build') {
            steps {
                sh 'go build -o app .'
            }
        }
        stage('Upload to Nexus') {
            steps {
                script {
                    def fileName = 'app'
                    def filePath = "${WORKSPACE}/${fileName}"
                    def nexusUploadUrl = "${NEXUS_URL}/repository/${NEXUS_REPO}/${fileName}"

                    withCredentials([usernamePassword(credentialsId: "${NEXUS_CREDENTIALS_ID}", usernameVariable: 'NEXUS_USER', passwordVariable: 'NEXUS_PASSWORD')]) {
                        sh """
                        curl -u ${NEXUS_USER}:${NEXUS_PASSWORD} --upload-file ${filePath} ${nexusUploadUrl}
                        """
                    }
                }
            }
        }
    }
}

```
4. Работает: https://github.com/timakhova/DevOps_CI_CD/blob/main/3-3%20done.png, https://github.com/timakhova/DevOps_CI_CD/blob/main/3-4%20done.png
