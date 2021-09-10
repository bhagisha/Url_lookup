 
 ## Problem Statement

URL-Lookup-Service

We have an HTTP proxy that is scanning traffic looking for malware URL's. Before allowing HTTP connections to be made, this proxy asks a service that maintains several databases of malware URL's if the resource being requested is known to contain malware.

Write a small web service, preferably in Java or Python, that responds to GET requests where the caller passes in a URL and the service responds with some information about that URL. The GET requests would look like this:

GET /urlinfo/1/{hostname_and_port}/{original_path_and_query_string}

The caller wants to know if it is safe to access that URL or not. You get to choose the response format and structure. These lookups are blocking users from accessing the URL until the caller receives a response from your service.

Give some thought to the following:

· The size of the URL list could grow infinitely, how might you scale this beyond the memory capacity of the system? Bonus if you implement this.

· The number of requests may exceed the capacity of this system, how might you solve that? Bonus if you implement this.

· What are some strategies you might use to update the service with new URLs? Updates may be as much as 5 thousand URLs a day with updates arriving ev0 minutes.

==========================================

## Solution

## Tools
To implement the  URL Lookup Service, I have selected following Tools

- Spring Boot framework for REST Microservice
- Mongo DB
- Enbeded Tomcat with SpringBoot framework  
- JUnit Test SpringBoot Test - MockMvc, SpringRunner etc

## Environment Setup

## Development Environment used: macOS 10.12


- Go to following GitHub Repository for the source code/configuration and download

	https://github.com/rahulda2018/URL-Lookup-Service

## Database:

	- Download the Community Edition
		https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/#install-mongodb-community-edition
	- Extract the files from the downloaded archive
		>tar -zxvf mongodb-osx-ssl-x86_64-3.6.4.tgz
		
	- Copy the extracted archive to the target directory
		>mkdir -p mongodb
		>cp -R -n mongodb-osx-ssl-x86_64-3.6.4/ mongodb
		
	- Ensure the location of the binaries is in the PATH variable
		>export PATH=<mongodb-install-directory>/bin:$PATH

	- Run the following command
		>mongod --dbpath=.

	- Now make sure mongo db services is running properly

## Download and install Robo 3T(robomongo) or Studio 3t for Mongo DB management tool.

	- MongoDB is running on local host on 27017 port

	- Refer application, properties file under src/main/resources folder

		#Mongo Config
		spring.application.name=URL List in Mongo DB
		spring.data.mongodb.hostname=localhost
		spring.data.mongodb.port=27017
		spring.data.mongodb.database=BlackListDB
		spring.data.mongodb.repositories.enabled=true



## Install Eclipse Java EE IDE for Web Developers.[Version: Oxygen.3a Release (4.7.3a)]
	
	- Import the the Spring Boot REST Microservice Application as Maven Project 
		File -> Import -> Maven Project -> pom.xml
	
	- Wait for all dependencies to be downloaded and resolved

## Run the following SpringBootApplication as Java Application
	com.java.app.urllookupservice.UrlLookupServiceApplication

	- Refer application, properties file under src/main/resources folder

		#Server Port
		server.port=9090

	- Embedded tomcat will be running on 9090

## Download Postman or Google Browser to verify the REST APIS

	GET /urlinfo/1/{hostname_and_port}/{original_path_and_query_string}

## REST API GUIDE


## Add URL into BlacklistDB
===============================

   Request:
	http://localhost:9090/add-to-blacklist/www.whalidgroup.com/user=bhagisha&empid=01234

   Response:
	{"hostName":"whalidgroup.com","fullURL":"user=rahul&empid=01234","severity":1,"description":"Malicious URL has beed added explicitly"}


## URL lookup - malware
============================

   Request:
	http://localhost:9090/urlinfo/1/whalidgroup.com/user=bhagisha&empid=04123/

   Response:
	{"result":[{"severity":1,"hostName":"whalidgroup.com","full-url":"user=bhagisha&empid=04123","description":"Malicious URL"}],"success":false,"message":"Malicious URL present in Blacklist DB"}

## URL Lookup - Good URL
==============================

   Request:
	http://localhost:9090/urlinfo/1/www.google.com/user=bhagisha&empid=04123/

   Response:
	{"result":[{"severity":0,"hostName":"google.com","full-url":"user=bhagisha&empid=04123","description":"URL not found in the database, need to be scanned.."}],"success":true}

## Marking a blacklisted URL as Whitelisted
================================================

   Request:
	http://localhost:9090/markAsWhiteListed/whalidgroup.com/user=bhagisha&empid=04123/

   Response:
	{"hostName":"whalidgroup.com","fullURL":"user=rahul&empid=04123","severity":0,"description":"URL has been marked as whitelisted explicitly"}

## Checking URL which are marked as as Whitelisted - now Good URL
=============================================================================

   Request:
	http://localhost:9090/urlinfo/1/whalidgroup.com/user=bhagisha&empid=04123/

   Response:
	{"result":[{"severity":0,"hostName":"whalidgroup.com","full-url":"user=bhagisha&empid=04123","description":"URL has been marked as whitelisted explicitly"}],"success":true}


## Unit Test Cases
====================

   Please Go to Eclipse and run the following class as JUnit Test (Run As -> Unit Test)
	
	Under src/test/java/ : com.java.app.urllookupservice.UrlLookupRestAppTests 


## Questions: Give some thought to the following:
======================================================


Q1. The size of the URL list could grow infinitely. How might you scale this beyond the memory capacity of the system?

Answer: Yes, this is common scenario. That’s why I. Have selected NoSQL Document DB(Mongo DB). We should use Elastic Search to scale this problem.

Q2. Assume that the number of requests will exceed the capacity of a single system, describe how might you solve this, and how might this change if you have to distribute this workload to an additional region, such as Europe.

Answer: We need to think for load based cluster approach or horizontal scaling.

Q3. What are some strategies you might use to update the service with new URLs? Updates may be as much as 5 thousand URLs a day with updates arriving every 10 minutes.

Answer: Here is the strategy -

Requested URL needs to be checked in Black listed DB first whether it exists or not, if exist report that URL Blacklisted If the URL is not present in the BlackList DB, then it should be submitted for analysis/detection, if severity level is higher(malware), then URL should be inserted into BlackListDB as malware. Unknown URL needs to be analysed if other services doesn’t know the reputation or URL doesn’t match with any access control rule or any other locos data set. Most of the times, cloud service/other service updates the data once per day depending on the traffic congestion. Auto update might helps with different scheduler (30 mins interval or weekly or day basis), but data should be always current.

Q4.You’re woken up at 3am, what are some of the things you’ll look for?

Answer : Firstly, i will look into bugs that have caused me to wake up, by going into the server logs, or by generating logs using tools like Log4J, then go through them and look for the error. Next, if the issue is urgent, ill start working on it right away, but if incase i have some time, then i will draft an email stating the issue and that i will start with the next morning.

Q5. Does that change anything you’ve done in the app?

Answer : If the app i have created is causing errors like crashing or anything, then definately i have to make changes for the same, so that it wont. Or if any update is suggested, then again i have to make changes, or another case for me to make changes, is that if someone has suggeted me a better way to do the same thing, i think thats the best way to learn new things, by welcoming reviews from peers and seniors.

Q6. What are some considerations for the lifecycle of the app?

Answer : Planning is the most crucial stage of the SDLC process. It involves identifying and defining the project scope to determine a comprehensive action plan for the project, and pinpointing the issues the solution it would resolve. We need to examine all phases of the SDLC with respect to application development, including: Inception, Design, Development, Stabilization, Deployment, and Maintenance

Q7. You need to deploy a new version of this application. What would you do?

Answer : 
**Use DOCKER**
1. application that matches environment's platform
2. Open the Elastic Beanstalk console, and in the Regions list, select your AWS Region.
3. In the navigation pane, choose Environments, and then choose the name of environment from the list.
4. On the environment overview page, choose Upload and deploy.
5. Choose Choose file, and then upload the sample application source bundle that you downloaded.The console automatically fills in the Version label with a new unique label. If you type in your own version label, ensure that it's unique.
6. Choose Deploy.
While Elastic Beanstalk deploys your file to your Amazon EC2 instances, you can view the deployment status on the environment's overview. While the application version is updated, the Environment Health status is gray. When the deployment is complete, Elastic Beanstalk performs an application health check. When the application responds to the health check, it's considered healthy and the status returns to green. The environment overview shows the new Running Version—the name you provided as the Version label.

Elastic Beanstalk also uploads your new application version and adds it to the table of application versions. To view the table, choose Application versions under getting-started-app on the navigation pane.

**Use SCCM**
Update the installation files in the original location.
Open SCCM Manager
Browse to the application you want to change. It’s usually in Software Library -> Overview -> Application Management -> Applications
Click and select the application.
Click on the Deployment Types in the lower left corner.
Right click on the item and select Update Content.


===================================================





