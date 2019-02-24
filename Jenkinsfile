node('slave') {
    stage('Compilando...') {
        echo 'Intalando Dependencias... '
        checkout scm //Jenkins with multibranch support
        sh "mvn clean install -U -Dmaven.test.skip=true -P local"
        
        echo 'Projeto Compilado com versao local =)'
    }
    stage('Testando...') {
        echo 'Iniciando Testes... '
        sh "mvn test -P local"
        echo 'Testes Unitarios Realizados'
        
        sh "mvn failsafe:integration-test -P local"
        echo 'Testes Integrados Realizados'
        
        echo 'Projeto Testado =)'
    }

    stage('Publicar JAR no Nexus?') {
        input message: "Deseja publicar esse build no Nexus?", ok: "Publicar JAR no Nexus!"
    }
    stage('Publicando JAR no Nexus...') {
        echo 'Publicando JAR no Nexus.... '
    
        sh "mvn clean install -U -P cloud"
        echo 'JAR versao cloud gerado '

        sh "mvn deploy -DskipTests"
        echo 'JAR versao cloud publicado no Nexus =)'
    }
    
    stage('Publicar Imagem Docker?') {
        input message: "Deseja publicar a Imagem Docker?", ok: "Publicar Docker!"
    }
    stage('Publicando Imagem Docker...') {


        echo 'Publicando Docker....'
        
        sh "docker login -u oauth2accesstoken -p \"\$(gcloud auth application-default print-access-token)\" https://gcr.io"
        echo 'Logado no Docker'
        
        sh "mvn package docker:build -DskipTests"
        echo 'Imagem Docker Gerada'

        sh "docker push us.gcr.io/previdenciasnsl/previdencia-capitalizacao"
        echo 'Imagem Docker Publicada no GCP =)'
    }
    
    stage('Publicar no ambiente DEV?') {
        input message: "Deseja publicar no ambiente DEV?", ok: "Publicar DEV!"
    }
}

node('ansible-master01') {    
    stage('Publicando no ambiente DEV...') {
    
        echo 'Publicando DEV....'
        sh "sudo ansible-playbook /export/infra-ansible/playbooks/applications/previdencia/previdencia.yml"

    }
}