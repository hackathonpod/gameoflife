pipeline{
    agent any
    stages{
        stage('git Checkout'){
            steps{  
                    checkout changelog: false, scm: [$class: 'GitSCM', branches: [[name: '*/develop']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/hackathonpod/gameoflife.git']]]
                }
            }
        stage('build'){
                steps{
                    withMaven(jdk: 'jdk', maven: 'Maven') {
                        sh "mvn clean install -DskipTests=true"
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
                script{
                    def branch_nem = scm.branches[0].name
                    def tmp = env.BRANCH_NAME
                    if (branch_nem.contains("*/")) {
                    branch_nem = branch_nem.split("\\*/")[1]
                    }
                    echo branch_nem
                    if(tmp == "release-1.0"){
                cloudBeesFlowTriggerRelease configuration: 'cd-configuration', parameters: '{"release":{"releaseName":"POD1_HACK_release1.1 Copy","stages":[{"stageName":"Dev","stageValue":false},{"stageName":"Prod","stageValue":false},{"stageName":"QA","stageValue":true}],"parameters":[{"parameterName":"ProjectKey","parameterValue":"POD2"},{"parameterName":"ProjectName","parameterValue":"POD2"},{"parameterName":"ProjectVersion","parameterValue":"1.0.0"},{"parameterName":"input_param","parameterValue":""},{"parameterName":"BranchName","parameterValue":"release-1.0"}]}}', projectName: 'hvora', releaseName: 'POD1_HACK_release1.1 Copy', startingStage: '' 
                    }
                    else if(tmp == "develop"){
                        cloudBeesFlowTriggerRelease configuration: 'cd-configuration', parameters: '{"release":{"releaseName":"POD1_HACK_release1.1 Copy","stages":[{"stageName":"Dev","stageValue":true},{"stageName":"Prod","stageValue":false},{"stageName":"QA","stageValue":false}],"parameters":[{"parameterName":"ProjectKey","parameterValue":"POD2"},{"parameterName":"ProjectName","parameterValue":"POD2"},{"parameterName":"ProjectVersion","parameterValue":"1.0.0"},{"parameterName":"input_param","parameterValue":""},{"parameterName":"BranchName","parameterValue":"develop"}]}}', projectName: 'hvora', releaseName: 'POD1_HACK_release1.1 Copy', startingStage: ''    
                    }
                    else if(tmp == "master"){
                        cloudBeesFlowTriggerRelease configuration: 'cd-configuration', parameters: '{"release":{"releaseName":"POD1_HACK_release1.1 Copy","stages":[{"stageName":"Dev","stageValue":true},{"stageName":"Prod","stageValue":false},{"stageName":"QA","stageValue":false}],"parameters":[{"parameterName":"ProjectKey","parameterValue":"POD2"},{"parameterName":"ProjectName","parameterValue":"POD2"},{"parameterName":"ProjectVersion","parameterValue":"1.0.0"},{"parameterName":"input_param","parameterValue":""},{"parameterName":"BranchName","parameterValue":"master"}]}}', projectName: 'hvora', releaseName: 'POD1_HACK_release1.1 Copy', startingStage: ''
                    }
                }
            }
    }
}
}

