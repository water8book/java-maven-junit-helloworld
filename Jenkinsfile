pipeline {
    options{
        timeout(time:1,unit:'HOURS')
    }
    environment {
        docker_image_name = "java8-maven3-junit5"
    }
    
    agent {
    dockerfile {
        additionalBuildArgs '--no-cache=true --build-arg "JENKINS_USER_ID=112" --build-arg "JENKINS_GROUP_ID=117"'
        args '-v ${PWD}/.m2:/usr/share/maven/.m2'
        dir '.'
        filename 'Dockerfile'
        label env.docker_image_name
        }
    }
    stages {
        stage('maven execution') {
            steps {
                script {
                    dir('.') {
                        sh 'set HTTP_PROXY=$HTTP_PROXY'
                        sh 'set HTTPS_PROXY=$HTTP_PROXY'
                        sh 'mvn clean package site'
                    }
                }
                gradlew 'test jacocoTestReport -x classes -x testClasses'

                junit "build/test-results/test/*.xml"
                archiveArtifacts "build/test-results/test/*.xml"

                step([
                    $class: '**/classes',
                    execPattern: "**/**.exec",
                ])
            }
            post {
                success {
                recordIssues tool: checkStyle(pattern: "**/checkstyle-result.xml")
                recordIssues tool: spotBugs(pattern: "**/spotbugsXml.xml")
                stepcounter settings: [[encoding: 'UTF-8', filePattern: 'src/main/**/*.java', filePatternExclude: '', key: 'Java'], [encoding: 'UTF-8', filePattern: 'src/test/**/*.java', filePatternExclude: 'src/test/java/sample/*.java', key: 'TestCode']]
                }
            }
        }
        stage('Analysis') {
            steps {
                script {
                    dir('.') {
                        sh 'echo "Analysis stage"'
                    }
                }
            }
        }
    }

}
