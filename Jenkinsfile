//properties([pipelineTriggers([pollSCM('* * * * *')])])
pipeline{
    agent any
    stages{
        stage('git Checkout'){
            steps{  
                    checkout changelog: false, scm: [$class: 'GitSCM', branches: [[name: '*/release-1.0']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/hackathonpod/gameoflife.git']]]
                }
            }
        stage('build'){
                steps{
                    withMaven(jdk: 'jdk', maven: 'Maven') {
                        sh "mvn clean package -DskipTests=true"
                    }
                    }
                }
        stage('DockerImage'){
            steps{  
                sh """cat << EOF > Dockerfile
                        FROM tomcat:8-jre11
                        RUN rm -rf /usr/local/tomcat/webapps/*
                        COPY target/gameoflife-0.0.2.war /usr/local/tomcat/webapps/ROOT.war
                        EXPOSE 8081
                        CMD ["catalina.sh", "run"]
                        EOF
                        docker build -t hackathonpod2/gameoflife .
                        docker login --username hackathonpod2 --password wipro@123
                        docker push hackathonpod2/gameoflife """        
                    }
            }   
            stage('CB TriggerRelease'){
            steps{  
                cloudBeesFlowTriggerRelease configuration: 'cd-configuration', parameters: '{"release":{"releaseName":"POD1_HACK_release1.1 Copy","stages":[{"stageName":"Dev","stageValue":false},{"stageName":"Prod","stageValue":false},{"stageName":"QA","stageValue":true}],"parameters":[{"parameterName":"input_param","parameterValue":""},{"parameterName":"BranchName","parameterValue":"release-1.0"}]}}', projectName: 'hvora', releaseName: 'POD1_HACK_release1.1 Copy', startingStage: ''    
            }
    }
}
}
