# URL lookup service 

######################
Challange
######################
We have an HTTP proxy that is scanning traffic looking for malware URLs. Before allowing HTTP connections to be made, 
this proxy asks a service that maintains several databases of malware URLs if the resource being requested is known to 
contain malware.

Part 1: Write a small web service that responds to GET requests where the caller passes in a URL and the service responds
 with some information about that URL. The GET requests would look like this:

  GET /urlinfo/1/{hostname_and_port}/{original_path_and_query_string}

The caller wants to know if it is safe to access that URL or not. As the implementer you get to choose the response format 
and structure. These lookups are blocking users from accessing the URL until the caller receives a response from your service. 
  
Part 2: As a thought exercise, please describe how you would accomplish the following:

• The size of the URL list could grow infinitely. How might you scale this beyond the memory capacity of the system? 

• Assume that the number of requests will exceed the capacity of a single system, describe how might you solve this, 
and how might this change if you have to distribute this workload to an additional region, such as Europe. 

• What are some strategies you might use to update the service with new URLs? Updates may be as much as 5 thousand URLs 
a day with updates arriving every 10 minutes.

• You’re woken up at 3am, what are some of the things you’ll look for?

• Does that change anything you’ve done in the app?

• What are some considerations for the lifecycle of the app?

• You need to deploy a new version of this application. What would you do?

######################
Info on URL Lookup Service
######################
URL_Lookup.py : Main backend module that parses URL from application request, looks up in mysql db for malwares
                returns the result with URL is safe or Malware.
update_db_with_malwares.py: Backend module to update service's database periodically with the info about new URLs or 
                updates on existing URLs.
URL_Lookup_WHOIS.py: Alternative URL info source WHOIS registars

URL_Lookup_App.py: Example Application using this URL Lookup service to verify it's safe to visit URL before visiting.
simple.html:    An html webform accepting URL and calls backend module with cgi field to consume the backend service.                                

######################
How to Use???
######################
1. Place the simple.html file under your webserver's config
    Example: MAMP ===>> /Applications/MAMP/htdocs
2. Checkout everything else and keep it under your webserver's cgi-bin config
    Example: MAMP ===>> /Applications/MAMP/cgi-bin

WS would look like this
bash-3.2$ ls -lrt
total 24
-rwxr-xr-x  1 bhagisha  admin  670 Sep  5 16:20 URL_Lookup_App.py
drwxr-xr-x  7 bhagisha  admin  238 Sep  5 16:33 URL-Lookup
bash-3.2$ pwd
/Applications/MAMP/cgi-bin


Questions: Give some thought to the following:
======================================================

Q1. The size of the URL list could grow infinitely. How might you scale this beyond the memory capacity of the system? 

Answer: Yes, this is common scenario. That’s why I. Have selected NoSQL Document DB(Mongo DB). We should use Elastic Search to scale this problem.

Q2. Assume that the number of requests will exceed the capacity of a single system, describe how might you solve this, and how might this change if you have to distribute this workload to an additional region, such as Europe. 

Answer: We need to think for load based cluster approach or horizontal scaling.

Q3. What are some strategies you might use to update the service with new URLs? Updates may be as much as 5 thousand URLs a day with updates arriving every 10 minutes.

Answer: Here is the strategy -

Requested URL needs to be checked in Black listed DB first whether it exists or not, if exist report that URL Blacklisted
If the URL is not present in the BlackList DB, then it should be submitted for analysis/detection, if severity level is higher(malware), then URL should be inserted into BlackListDB as malware.
Unknown URL needs to be analysed if other services doesn’t know the reputation or URL doesn’t match with any access control rule or any other locos data set.
Most of the times, cloud service/other service updates the data once per day depending on the traffic congestion. Auto update might helps with different scheduler (30 mins interval or weekly or day basis), but data should be always current.

Q4.You’re woken up at 3am, what are some of the things you’ll look for?

Answer : Firstly, i will look into bugs that have caused me to wake up, by going into the server logs, or by generating logs using tools like Log4J, then go through them and look for the error. Next, if the issue is urgent, ill start working on it right away, but if incase i have some time, then i will draft an email stating the issue and that i will start with the next morning.

Q5. Does that change anything you’ve done in the app?

Answer : If the app i have created is causing errors like crashing or anything, then definately i have to make changes for the same, so that it wont. Or if any update is suggested, then again i have to make changes, or another case for me to make changes, is that if someone has suggeted me a better way to do the same thing, i think thats the best way to learn new things, by welcoming reviews from peers and seniors.

Q6. What are some considerations for the lifecycle of the app?

Answer : Planning is the most crucial stage of the SDLC process. It involves identifying and defining the project scope to determine a comprehensive action plan for the project, and pinpointing the issues the solution it would resolve. We need to examine all phases of the SDLC with respect to application development, including: Inception, Design, Development, Stabilization, Deployment, and Maintenance

Q7. You need to deploy a new version of this application. What would you do?

Answer : Use DOCKER

application that matches environment's platform
Open the Elastic Beanstalk console, and in the Regions list, select your AWS Region.
In the navigation pane, choose Environments, and then choose the name of environment from the list.
On the environment overview page, choose Upload and deploy.
Choose Choose file, and then upload the sample application source bundle that you downloaded.The console automatically fills in the Version label with a new unique label. If you type in your own version label, ensure that it's unique.
Choose Deploy. While Elastic Beanstalk deploys your file to your Amazon EC2 instances, you can view the deployment status on the environment's overview. While the application version is updated, the Environment Health status is gray. When the deployment is complete, Elastic Beanstalk performs an application health check. When the application responds to the health check, it's considered healthy and the status returns to green. The environment overview shows the new Running Version—the name you provided as the Version label.
Elastic Beanstalk also uploads your new application version and adds it to the table of application versions. To view the table, choose Application versions under getting-started-app on the navigation pane.

Use SCCM Update the installation files in the original location. Open SCCM Manager Browse to the application you want to change. It’s usually in Software Library -> Overview -> Application Management -> Applications Click and select the application. Click on the Deployment Types in the lower left corner. Right click on the item and select Update Content.
