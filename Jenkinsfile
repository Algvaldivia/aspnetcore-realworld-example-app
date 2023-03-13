// Test Jenkins with SonarQube
pipeline {
    environment {
        target = "${WORKSPACE}/target"
        sboms = "${target}/syft"
        vulnreports = "${target}/grype"
        build_string = "${BRANCH_NAME}-${BUILD_ID}"
    }
    agent {
        label 'docker-linux'
    }
    stages {
        stage('SCM') {
            git 'https://github.com/Algvaldivia/aspnetcore-realworld-example-app.git'
        }
        stage('Checkout') {
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
        }

        stage('SonarQube with SonarScannerCLI') {
            steps {
                withSonarQubeEnv(installationName: 'SonarQube (172.1.203.230)', envOnly: true) {
                    // This expands the evironment variables SONAR_CONFIG_NAME, SONAR_HOST_URL, SONAR_AUTH_TOKEN that can be used by any script.
                    println "${env.SONAR_HOST_URL}"
                    println "${env.SONAR_AUTH_TOKEN}"
                    println "${env.SONAR_KEY}"
                    println "${env.SONAR_CONFIG_NAME}"
                    echo "${WORKSPACE_TMP}"
                    sh label: 'Create SonarQube Cache Folder', script: "mkdir -p /jenkins_home/.sonar/cache"
                    sh "docker run --interactive \
                        --volume ${WORKSPACE}:/usr/src \
                        --volume /jenkins_home/.sonar/cache:/opt/sonar-scanner/.sonar/cache \
                        --rm sonarsource/sonar-scanner-cli \
                        -D sonar.verbose=true \
                        -D sonar.host.url=${SONAR_HOST_URL} \
                        -D sonar.projectKey=${JOB_BASE_NAME} \
                        -D sonar.projectName=${JOB_NAME} \
                        -D sonar.projectVersion=${BUILD_NUMBER} \
                        -D sonar.sources=/usr/src \
                        -D sonar.login=${SONAR_AUTH_TOKEN} \
                    "
                }
            }
        }

        stage('SonarQube Jenkins Plugin') {
            steps {
                echo "${BRANCH_NAME}"
                /* //withSonarQubeEnv(installationName: 'SonarQube (172.1.203.230)') {
                    // This expands the evironment variables SONAR_CONFIG_NAME, SONAR_HOST_URL, SONAR_AUTH_TOKEN that can be used by any script.
                    // println "${env.SONAR_HOST_URL}"
                    println "${env.SONAR_AUTH_TOKEN}"
                    println "${env.SONAR_CONFIG_NAME}"
                    //sh "npm install -D typescript"
                    // sudo apt install maven
                    sh 'mvn clean package sonar:sonar'
                } */
            }
        }

        stage("Quality Gate") {
            steps {
                echo "Hello Quality Gate"
            }
        }
    }
}
