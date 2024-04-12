pipeline {
    agent any
    parameters {
        string(name: 'BRANCH', defaultValue: 'uat', description: 'The branch to build')
    }
    environment {
        BRANCH_NAME = "${params.BRANCH}"
    }
    stages {
        stage('build') {
            steps {
                script {
                    deleteDir()
                    withCredentials([aws(credentialsId: 'was Jenkins-usr')]) {
                        withEnv(['MY_ENV_VAR=my_value', 'MY_ENV_VAR1=my_value1']) {
                            echo 'Code checkout'
                            checkout([
                        $class: 'GitSCM', 
                        branches: [[name: '*/${BRANCH}']],
                        doGenerateSubmoduleConfigurations: false,
                        extensions: [
                            [$class: 'RelativeTargetDirectory', relativeTargetDir: 'app'],
                            [$class: 'CloneOption', shallow: true, noTags: false, reference: '', depth: 1, timeout: 1]
                        ],
                        submoduleCfg: [],
                        userRemoteConfigs: [[
                            credentialsId: 'f8f2ba9d-98e2-44fb-88fd-1cq3rco08nlh',
                            url: 'https://github.com/mayankjain-sudo/shellscript-automation.git'
                        ]]
                    ])
                        }
                    }
                }
            }
        }
        stage('Copy artifacts') {
            steps {
                copyArtifacts projectName: '/learning/scriptJob', selector: lastSuccessful(), target: 'app', fingerprintArtifacts: true
            }
        }
        stage('Read variables') {
            steps {
                script {
                    // Read the value of a key from a yaml file
                    def data = readYaml(file: 'app/vars.yaml')
                    def env_name = data[BRANCH_NAME]['env']
                    def app_name = data[BRANCH_NAME]['app']
                    echo  "App Name: ${app_name}"
                    sh 'ls app'
                    sh 'bash app/appinput.sh'
                    sh 'chmod 755 app/appinput.sh app/bin/appbuild.sh'
                    sh "bash app/bin/appbuild.sh 'app/appinput.sh'"
                }
            }
        }
    }
}
