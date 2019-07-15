
node {
    
    /*            GIT Details              */
    def gitUrl = 'git@github.com:vernekarakshata/Inventory-Jenkins.git'
    def branchName = 'master'
//    def gitCredentialId = '5bd19c27-30a1-42d9-9f82-2d62eb81e2ac'
    
    /*            Maven & Sonar Details             */
    def mavenToolName = 'MAVEN_HOME';
    def mavenHome = tool "${mavenToolName}";
    def sonarServerName = 'SonarQube';
    def sonarQuery = 'clean install sonar:sonar';
    def pom = 'pom.xml'
    
    /*            JFrog Artifactory Details             */
    def artifactoryMavenGoals = 'clean install'
    def artifactoryServerId = 'JFROG_ARTIFACTORY'
    def resolverId = 'repo-id'
    def resolverReleaseRepo = 'pipeline-project-repo'
    def resolversnapshotRepo = 'pipeline-project-repo'
    def deployerId = 'deploy-id' 
    def deployerReleaseRepo = 'pipeline-project-repo' 
    def deployersnapshotRepo = 'pipeline-project-repo'
    
    /*            Docker Details             */
    def registry = "vernekarakshata/inventory-management"
    def registryCredential = 'c262e8a9-1c29-41fd-859d-85e0c520777e'
    def tagName = '$BUILD_NUMBER'
    
    /*            Deployment Details             */
    def source = "target/InventoryManagement.war"
    def user = "ubuntu"
    def hostname = "ec2-3-17-4-149.us-east-2.compute.amazonaws.com"
    def destination = "/home/ubuntu/tomcat/webapps/"
    
    /*            Selenium Project Details             */
    def seleniumJob = 'Inventory-Selenium-Framework'
    
    /*            Email Details             */
    def subject = 'Project ${env.JOB_NAME} deployment \'${env.currentBuild.result}\''
    def to = 'akshatav10@gmail.com'
    def from = '' 
    def cc = '' 
    def bcc = '' 
    def replyTo = ''
    
    try{
        
        stage('GIT Checkout') {
            gitCheckout(gitUrl,branchName)
        }
        stage('SonarQube analysis and Quality Gate') {
           withSonarQubeEnv(sonarServerName) {
                sonarqubeAnalysis(mavenHome,sonarQuery)
            }
            checkQualitygate();
        }
        stage('Maven Build and Deploy the artifacts and publish build info'){
            mavenBuildArtifactoryDeployment(mavenToolName, pom, artifactoryMavenGoals, artifactoryServerId,  
            resolverId, resolverReleaseRepo, resolversnapshotRepo,
            deployerId, deployerReleaseRepo, deployersnapshotRepo)
        }
        stage('Create Docker Image & Publich to Docker Hub'){
            dockerBuildImagePushToHub(registry, tagName, registryCredential);
        }
        stage('Deploy to Server'){
            deployToServer(source, user, hostname, destination)
        }
        stage('Execute Automation Test Suites'){
            build job: "${seleniumJob}"
        }
   
    }finally{
        sendMail(to, from, cc, bcc, replyTo)
        
    }

}