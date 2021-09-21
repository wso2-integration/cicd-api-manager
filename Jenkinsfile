pipeline {

    agent {
        node {
            label 'master'
        }
    }
    environment { 
        PATH = "/root/apictl:$PATH"
        ADMIN = credentials('admin_user')
    }
    options {
        buildDiscarder logRotator( 
                    daysToKeepStr: '16', 
                    numToKeepStr: '10'
            )
    }

    stages {

        stage('Setup Environment for APICTL') {
            steps {
                sh """#!/bin/bash
                ENVCOUNT=\$(apictl list envs --format {{.}} | wc -l)
                if [ "\$ENVCOUNT" == "0" ]; then
                    apictl add-env -e dev --apim https://dev.mycorp.com:9443
                fi
                """
            }
        }

        stage('Deploy APIs To "Dev" Environment') {
            steps {
                sh """
                apictl login dev -u \$ADMIN_USR -p \$ADMIN_PSW -k
                apictl vcs deploy -e dev
                """
            }
        }
    }
    post { 
        always { 
            sh """
            apictl logout dev
            """
        }
    }
}
