pipeline {
    agent any

    stages {
        stage('Download EAR from Nexus') {
            steps {
                script {
                    currentBuild.displayName = "${Stage_Version}"
                    artifactResolver(artifacts: [artifact(artifactId: '${Stage_Artifact_ID}', extension: 'ear', groupId: '${Stage_Group_ID}', targetFileName: '${appname}.ear', version: '${Stage_Version}')])
                }
            }
        }
        
        stage('Stage to System Test') {
            steps {
                script {
                    sshPublisher(publishers: [sshPublisherDesc(configName: 'LAADS SYST Deployment', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: '', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '*.ear')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
                }
            }
        }
        
        stage('Move Ear File') {
            steps {
                withCredentials([usernamePassword(credentialsId: '52ce6b99-ed7f-460f-ad20-13a8c4c3adc6', passwordVariable: 'password', usernameVariable: 'username')]) {
                    bat label: '', script: 'curl -u DOTStaging:INPUT_PASSWORD -X POST "https://dotnexusprodp.dot.state.wi.us/service/rest/v1/staging/move/dot-uat?name=%appname%*&version=%Stage_Version%"'
                }
            }
        }
        
        stage('log') {
            steps {
                bat label: '', script: '''echo. >> E:\\was9logs\\Stage\\%appname%.txt
echo ====================================== >> E:\\was9logs\\Stage\\%appname%.txt
echo. >> E:\\was9logs\\Stage\\%appname%.txt
type "C:\\Program Files (x86)\\CloudBeesJenkinsDistribution\\jobs\\dtsd\\jobs\\%appname%\\builds\\%BUILD_ID%\\log" >> E:\\was9logs\\Stage\\%appname%.txt
echo. >> E:\\was9logs\\Stage\\%appname%.txt
echo ====================================== >> E:\\was9logs\\Stage\\%appname%.txt
echo. >> E:\\was9logs\\Stage\\%appname%.txt'''
            }
        }
        
        stage('Approval') {
            steps {
                input "Deploy to Acceptance?"
            }
        }
        
        stage('Stage to Acceptance') {
            steps {
                script {
                    sshPublisher(publishers: [sshPublisherDesc(configName: 'LAADS ACPT Deployment', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: '', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '*.ear')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
                }
            }
        }
        
        stage('log') {
            steps {
                bat label: '', script: '''echo. >> E:\\was9logs\\Stage\\%appname%.txt
echo ====================================== >> E:\\was9logs\\Stage\\%appname%.txt
echo. >> E:\\was9logs\\Stage\\%appname%.txt
type "C:\\Program Files (x86)\\CloudBeesJenkinsDistribution\\jobs\\dtsd\\jobs\\%appname%\\builds\\%BUILD_ID%\\log" >> E:\\was9logs\\Stage\\%appname%.txt
echo. >> E:\\was9logs\\Stage\\%appname%.txt
echo ====================================== >> E:\\was9logs\\Stage\\%appname%.txt
echo. >> E:\\was9logs\\Stage\\%appname%.txt'''
            }
        }
    }

    post {
        always {
            cleanWs()
            deleteDir()
        }
    }
}
