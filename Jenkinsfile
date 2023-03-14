// Test Jenkins with SonarQube
pipeline {
    environment {
        target = "${WORKSPACE}/target"
        sboms = "${target}/syft"
        vulnreports = "${target}/grype"
        build_string = "${BRANCH_NAME}-${BUILD_ID}"
    }
    agent {
        label 'dockerwin'
    }
    stages {
        /* stage('Checkout') {
            steps {
                // Get some code from a GitHub repository
               echo "${BRANCH_NAME}"
            }
        }
        stage('Syft Create SBOMs') {
            steps {
                // Get some code from a GitHub repository
                // https://github.com/anchore/syft#configuration
                echo "${sboms}"
                echo "${WORKSPACE}"
                echo "/home/syft/${BUILD_TAG}.json"
                echo "./usr/src/target/*"
                sh label: 'Create SBOM Folder', script: "mkdir -p ${sboms}"
                sh label: "Run docker Syft", script: "\
                    docker run --interactive\
                    --volume ${sboms}:/home/syft\
                    --volume ${WORKSPACE}:/usr/src\
                    --rm anchore/syft\
                    dir:/usr/src\
                    --output spdx-json\
                    --file /home/syft/${BUILD_TAG}.json\
                    --exclude ./usr/src/target/*\
                    "
            }
        }
        stage('Grype Scan with SBOM') {
            steps {
                sh label: 'Create Report Folder', script: "mkdir -p ${vulnreports}"
                sh label: 'GrypeScan', script: "\
                    docker run --interactive\
                    --volume ${sboms}:/home/syft\
                    --volume ${vulnreports}:/home/grype\
                    --volume ${WORKSPACE}:/usr/src\
                    --rm anchore/grype\
                    sbom:/home/syft/${BUILD_TAG}.json\
                    --file /home/grype/${BUILD_TAG}.json\
                    --output json"

               // '/usr/local/bin/anchore/grype dir:${WORKSPACE}  --file ${vulnreports}/${BUILD_TAG}.json -o json'
               // sh label: 'GrypeScan', script: '/usr/local/bin/anchore/grype dir:${WORKSPACE}  --file ${vulnreports}/${BUILD_TAG}.json -o json'
            }
            post {
                success {
                    // One or more steps need to be included within each condition's block.
                    // archiveArtifacts artifacts: 'target/vulnreports/*.json', followSymlinks: false
                    echo "OK"
                }
            }
        } */

/*         stage('SonarQube with Linux SonarScannerCLI') {
            steps {
                withSonarQubeEnv(installationName: 'SonarQube (172.1.203.230)', envOnly: true) {
                    // This expands the evironment variables SONAR_CONFIG_NAME, SONAR_HOST_URL, SONAR_AUTH_TOKEN that can be used by any script.
                    println "${env.SONAR_HOST_URL}"
                    println "${env.SONAR_AUTH_TOKEN}"
                    println "${env.SONAR_KEY}"
                    println "${env.SONAR_CONFIG_NAME}"
                    
                    sh label: 'Create SonarQube Cache Folder', script: "mkdir -p /jenkins_home/.sonar/cache"

                    // sh label: 'dotnet tool install', script: "\
                    // dotnet tool install --global dotnet-sonarscanner \

                    sh label: 'Set PATH', script: '\
                        export PATH=$PATH:/root/.dotnet \
                        export PATH=$PATH:/root/.dotnet/tools \
                        echo $PATH \
                    '
                    sh label: "SonarScanner.MSBuild.exe begin", script: "\
                        dotnet sonarscanner \
                        begin /k:${JOB_BASE_NAME} \
                        /name:${JOB_NAME} \
                        /version:${BUILD_NUMBER} \
                        /d:sonar.login=${SONAR_AUTH_TOKEN} \
                        /d:sonar.verbose=true \
                        /d:sonar.host.url=${SONAR_HOST_URL} \
                    "
                    sh label: "MSBuild.exe", script: "\
                        dotnet build ${WORKSPACE} \
                    "
                    sh label: "SonarScanner.MSBuild.exe end", script: "\
                        dotnet sonarscanner end /d:sonar.login=${SONAR_AUTH_TOKEN} \
                    "
                }
            }
        } */

        stage('SonarQube with Windows SonarScanner.MSBuild.dll"') {
            steps {
                withSonarQubeEnv(installationName: 'SonarQube (172.1.203.230)', envOnly: true) {
                    // This expands the evironment variables SONAR_CONFIG_NAME, SONAR_HOST_URL, SONAR_AUTH_TOKEN that can be used by any script.
                    println "${env.SONAR_HOST_URL}"
                    println "${env.SONAR_AUTH_TOKEN}"
                    println "${env.SONAR_KEY}"
                    println "${env.SONAR_CONFIG_NAME}"
                    
               /*      sh label: 'dotnet tool install', script: "\
                        dotnet tool install --global dotnet-sonarscanner \
                    " */

                    powershell """
                        Write-Output ${JOB_BASE_NAME}
                    """
                    echo "dotnet \'C:/ProgramData/SonarScanner for .NET 5+/SonarScanner.MSBuild.dll\' begin /k:${JOB_BASE_NAME} /name:${JOB_NAME} /version:${BUILD_NUMBER} /d:sonar.login=${SONAR_AUTH_TOKEN} /d:sonar.verbose=true /d:sonar.host.url=${SONAR_HOST_URL}"
                    powershell label: "SonarScanner.MSBuild.exe begin", script: """
                        dotnet \'C:/ProgramData/SonarScanner for .NET 5+/SonarScanner.MSBuild.dll\' begin /k:${JOB_BASE_NAME} /name:${JOB_NAME} /version:${BUILD_NUMBER} /d:sonar.login=${SONAR_AUTH_TOKEN} /d:sonar.verbose=true /d:sonar.host.url=${SONAR_HOST_URL}
                    """
                        // Set-Location -Path \"\$env:ProgramData\"
                    powershell label: "MSBuild.exe", script: """
                        dotnet build \"${WORKSPACE}\"
                    """
                    powershell label: '"SonarScanner.MSBuild.exe end"', script: """
                        dotnet \'C:/ProgramData/SonarScanner for .NET 5+/SonarScanner.MSBuild.dll\' end /d:sonar.login=${SONAR_AUTH_TOKEN}
                    """

                }
            }
        }

    }
}
