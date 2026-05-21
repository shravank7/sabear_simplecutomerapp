pipeline {
    agent any
    tools {
        // Note: this should match with the tool name configured in your jenkins instance (JENKINS_URL/configureTools/)
        maven "M2_HOME"
        
    }
	 environment {
        // This can be nexus3 or nexus2
        NEXUS_VERSION = "nexus3"
        // This can be http or https
        NEXUS_PROTOCOL = "http"
        // Where your Nexus is running
        NEXUS_URL = "13.218.188.180:8081/"
        // Repository where we will upload the artifact
        NEXUS_REPOSITORY = "Simplecustomerapp"
        // Jenkins credential id to authenticate to Nexus OSS
        NEXUS_CREDENTIAL_ID = "f8aec3dd-80dd-4369-8a7f-7b3971777fe2"
	SCANNER_HOME = tool '/opt/sonar_scanner'
    }
    stages {
        stage("clone code") {
            steps {
                script {
                    // Let's clone the source
                    git 'https://github.com/shravank7/sabear_simplecutomerapp.git';
                }
            }
        }
        stage("m2 build") {
            steps {
                script {
                    // If you are using Windows then you should use "bat" step
                    // Since unit testing is out of the scope we skip them
                    sh 'mvn -Dmaven.test.failure.ignore=true clean install'
                }
            }
        }
	stage('SonarCloud') {
            steps {
                withSonarQubeEnv('sonarqube_server') {
				sh '$SCANNER_HOME/bin/sonar-scanner \
				-Dsonar.projectKey=Ncodeit \
				-Dsonar.projectName=Ncodeit \
				-Dsonar.projectVersion=2.0 \
				-Dsonar.sources=/var/lib/jenkins/workspace/$JOB_NAME/src/ \
				-Dsonar.binaries=target/classes/com/visualpathit/account/controller/ \
				-Dsonar.junit.reportsPath=target/surefire-reports \
				-Dsonar.jacoco.reportPath=target/jacoco.exec \
				-Dsonar.java.binaries=src/com/room/sample '
				
		     }
		}
	    }
        stage("publish to nexus") {
            steps {
                script {
                    // Read POM xml file using 'readMavenPom' step , this step 'readMavenPom' is included in: https://plugins.jenkins.io/pipeline-utility-steps
                    pom = readMavenPom file: "pom.xml";
                    // Find built artifact under target folder
                    filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
                    // Print some info from the artifact found
                    echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
                    // Extract the path from the File found
                    artifactPath = filesByGlob[0].path;
                    // Assign to a boolean response verifying If the artifact name exists
                    artifactExists = fileExists artifactPath;
                    if(artifactExists) {
                        echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version}";
                        nexusArtifactUploader(
                            nexusVersion: nexus3,
                            protocol: http,
                            nexusUrl: 13.218.188.180:8081/,
			    groupId: com.javatpoint,
                            version: ${BUILD_NUMBER}-SNAPSHOT,
                            repository: Simplecustomerapp,
                            credentialsId: f8aec3dd-80dd-4369-8a7f-7b3971777fe2
,
                            artifacts: [
                                // Artifact generated such as .jar, .ear and .war files.
                                [artifactId: maven-war-plugin,
                                classifier: '',
                                file: artifactPath,
                                type: pom.packaging],
                                // Lets upload the pom.xml file for additional information for Transitive dependencies
                                [artifactId: pom.artifactId,
                                classifier: '',
                                file: "pom.xml",
                                type: "pom"]
                            ]





							pipeline {
    agent any

    tools {
        maven "M2_HOME"
    }

    environment {
        SCANNER_HOME = tool 'sonar_scanner'
    }

    stages {

        stage('Clone Code') {
            steps {
                git 'https://github.com/shravank7/sabear_simplecutomerapp.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarqube_server') {

                    sh """
                        mkdir -p target/classes

                        ${SCANNER_HOME}/bin/sonar-scanner \
                        -Dsonar.projectKey=Ncodeit \
                        -Dsonar.projectName=Ncodeit \
                        -Dsonar.sources=src \
                        -Dsonar.java.binaries=target/classes
                    """
                }
            }
        }

        stage('Upload Artifact to Nexus') {
            steps {

                nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: '13.218.188.180:8081',
                    repository: 'Simplecustomerapp',
                    credentialsId: 'f8aec3dd-80dd-4369-8a7f-7b3971777fe2',
                    groupId: 'com.javatpoint',
                    version: "${BUILD_NUMBER}-SNAPSHOT",

                    artifacts: [
                        [
                            artifactId: 'simplecustomerapp',
                            classifier: '',
                            file: 'target/SimpleCustomerApp-${BUILD_NUMBER}-SNAPSHOT.war',
                            type: 'war'
                        ]
                    ]
                )
            }
        }
    }
}
                        );
                    } else {
                        error "*** File: ${artifactPath}, could not be found";
                    }
                }
            }
        }
    }
}
