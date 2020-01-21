pipeline
{
	agent any
	environment
	{
		scannerHome = tool name: 'sonarscanner_nagp_dotnet', type: 'hudson.plugins.sonar.MsBuildSQRunnerInstallation'   
	}
	options
   {
      timeout(time: 1, unit: 'HOURS')
      
      // Discard old builds after 5 days or 5 builds count.
      buildDiscarder(logRotator(daysToKeepStr: '5', numToKeepStr: '5'))
	  
	  //To avoid concurrent builds to avoid multiple checkouts
	  disableConcurrentBuilds()
   }
		 
	stages
	{
		stage ('checkout')
		{
			steps
			{
				echo  " ********** Clone starts ******************"
				checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], gitTool: 'default', submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/siddhannt-arora/nagp2019.git']]])
			}
		}
		stage ('nuget')
		{
			steps
			{
				echo "************ restoring dependancies **********"
				bat "dotnet restore"
			}
		}
		stage ('Start sonarqube analysis')
		{
			steps
			{
				echo "*********** starting sonar analysis ***********"    
				
			}
		}
		stage ('build')
		{
			steps
			{
				echo "************* building the solution **********"
				bat "dotnet build -c Release -o WebApplication4/app/build"
			}	
		}
		stage ('SonarQube Analysis end')
		{	
			steps
			{
				echo "*************** Ending Sonar analysis ***********"
				
			}
		}
		stage ('Release Artifacts')
		{
			steps
			{
				echo "************** Publishing app ***************"
				bat "dotnet publish -c Release -o WebApplication4/app/publish"
			}
		}
		stage ('Docker Image')
		{
			steps
			{
				echo "****************** Build Docker image ****************"
				bat "docker build --no-cache -t siddhanntarora/nagp_3149656:${BUILD_NUMBER} ."
				
			}
		}
		stage ('Push to Docker Hub')
		{
			steps
			{
				echo "***************** Pushing image to Nagarro DTR or Docker Hub **********"
				withDockerRegistry(credentialsId:'279ed781-7ab1-4d46-a3e9-57c42c42bf34', url:'') {
					bat "docker push siddhanntarora/nagp_3149656:${BUILD_NUMBER}" 
				}
				
			}
			
		}
		stage ('Stop Running container')
		{
			steps
			{
				echo "*************** Removing already running conatiners *****************"
				
				
			}
		}
		stage ('Docker deployment')
		{
			steps
			{
			   echo "*************** Deploying latest war on Docker Containers **************"
			   bat "docker run --name dotnetcoreapp_siddhanntarora -d -p 5600:5600 siddhanntarora/nagp_3149656:${BUILD_NUMBER}"
			}
		}
	}

	 post {
			always 
			{
				echo "*********** Executing post tasks like Email notifications *****************"
			}
		}
}
