pipeline{
    agent any
    stages{
        stage('git Checkout'){
            step{  
                    checkout changelog: false, scm: [$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/hackathonpod/gameoflife.git']]]
                }
            }
        stage('build'){
                step{
                    withMaven(jdk: 'jdk', maven: 'Maven') {
                        sh "mvn clean package"
                    }
                }
        stage('DockerImage'){
            step{  
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
            step{  
                cloudBeesFlowTriggerRelease configuration: 'cd-configuration', parameters: '{"release":{"releaseName":"POD1_HACK_release1.1 Copy","stages":[{"stageName":"Dev","stageValue":true},{"stageName":"Prod","stageValue":false},{"stageName":"QA","stageValue":true}],"parameters":[{"parameterName":"input_param","parameterValue":""}]}}', projectName: 'hvora', releaseName: 'POD1_HACK_release1.1 Copy', startingStage: 'Dev'        
            }
            }
            }
        
    }
}