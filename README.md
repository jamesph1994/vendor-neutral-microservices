# Developing, Integrating and Protecting Vendor Neutral Microservices Demo Script #

This script demonstrated how a microservice can be developed using [Twelve-Factor](https://12factor.net/) principals, integrated using Oracle Integration Cloud Service and protected using the Oracle API Platform.

![](/images/overview.PNG)


## Pre-requisites ##
You have the following cloud accounts:

- Oracle [Application Container](https://cloud.oracle.com/en_US/application-container-cloud) Cloud Service
- Oracle [Integration](https://cloud.oracle.com/en_US/integration) Cloud Service
- Oracle [API Platform](https://cloud.oracle.com/en_US/api-platform) Cloud Service
- [Apiary](https://apiary.io/plans) Pro Account
- [Google Cloud](https://cloud.google.com) Account
- A [GitHub](https://github.com/) account.
- You have [Git](https://git-scm.com/) installed.
- You have [Node.js](https://nodejs.org/) installed.

## Setup
### Deploy the Library Microservice to ACCS
The Library Microservice is part of a microservices “container” application that has several microservices. The microservices are combined into a single application to conserve cloud resources. A single deployment can support a growing library of demo use cases.

#### Get the Node API Container Running Locally
- Fork the [https://github.com/wbleonard/node-api-container](https://github.com/wbleonard/node-api-container) to your GitHub account.
- Clone the https://github.com/{your-github-id}/node-api-container to your local workstation:

	```
	{ ~ }  » git clone https://github.com/{your-github-id}/node-api-container.git
	Cloning into 'node-api-container'...
	remote: Counting objects: 323, done.
	remote: Compressing objects: 100% (9/9), done.
	remote: Total 323 (delta 2), reused 0 (delta 0), pack-reused 314
	Receiving objects: 100% (323/323), 95.86 KiB | 0 bytes/s, done.
	Resolving deltas: 100% (197/197), done.
	Checking connectivity... done.
	```

- Install the application’s node modules:

	```
	{ node-api-container } master » npm install
	```

- Start the app:

	```
	{ node-api-container } master » node app.js
	Example app listening on port 8080!
	```

- Test in your browser:
 
	![](https://github.com/OracleNATD/vendor-neutral-microservices/blob/master/images/home-page.png)

#### Set up Continuous Integration

- In either a new or existing Developer Cloud Service project, select Administration > Repositories
- Create a **New External Repository** and set the URL to **https://github.com/{your-github-id}/node-api-container.git**. Optionally set the Description to something like ** A generic Node Express container application for creating APIs to be used in demonstrations.**

	![](https://github.com/OracleNATD/vendor-neutral-microservices/blob/master/images/new-repository.PNG)

- Create a New free-style Build Job named **Node API Container**

 	![](https://github.com/OracleNATD/vendor-neutral-microservices/blob/master/images/new-job.PNG)

	- Under **Source Control**, select the node-api-container repository:
 		![](https://raw.githubusercontent.com/OracleNATD/vendor-neutral-microservices/master/images/source-control.PNG)

	- Under **Triggers** select Based on SCM polling schedule:
	 
		![](https://raw.githubusercontent.com/OracleNATD/vendor-neutral-microservices/master/images/triggers.PNG)

	- Under **Build Steps** add an Execute shell Build Step and set the Command to **npm install**:
	 
		![](https://raw.githubusercontent.com/OracleNATD/vendor-neutral-microservices/master/images/build-steps.PNG)	

	- Under **Post Build**, select Archive the Artifacts and set the Files to Archive to ***.zip**:
 
		![](https://raw.githubusercontent.com/OracleNATD/vendor-neutral-microservices/master/images/post-build.PNG)	
	- **Save** and click **Build Now** to validate the build configuration:
	
 		![](https://raw.githubusercontent.com/OracleNATD/vendor-neutral-microservices/master/images/build-history.PNG)

#### Set up Continuous Deployment
	
- Create a new Deployment Configuration named **NodeAPIContainer**

- Create a new Application Container Cloud Deployment Target:

	![](https://raw.githubusercontent.com/OracleNATD/vendor-neutral-microservices/master/images/deployment-target.PNG)

- Set the remaining Deployment Configuration properties:
	- ACCS Properties: **Node**
	- Type: Automatic, selecting to **Deploy stable builds only**
	- Job: **Node API Container**
	- Artifact: **node-api-container.zip**

	![](https://raw.githubusercontent.com/OracleNATD/vendor-neutral-microservices/master/images/deployment-configuration.PNG)

#### Validate

- On your local workstation, make a change to the node-api-container source. I like to change the payload in instructionalRouter.js.

- Commit and Push the change. You can do this from Eclipse, NetBeans, [SoruceTree](https://www.sourcetreeapp.com/), etc. I prefer to use the command line:

	![](https://raw.githubusercontent.com/OracleNATD/vendor-neutral-microservices/master/images/git-commit.PNG)

- Validate that the commit triggers a build and successful deploy.

### Orchestrate the Library Microservice
This next step uses Oracle [Integration](https://cloud.oracle.com/en_US/integration) Cloud Service (ICS) to automatically resubmit the request, instructing the service to relax its search requirements by adding a query parameter to the service call. For example:

[https://nodeapicontainer-gse00001975.apaas.em2.oraclecloud.com/instructional/instructors/disciplines/math](https://nodeapicontainer-gse00001975.apaas.em2.oraclecloud.com/instructional/instructors/disciplines/math)

returns 0 records. However, 

[https://nodeapicontainer-gse00001975.apaas.em2.oraclecloud.com/instructional/instructors/disciplines/math?relax=true
](https://nodeapicontainer-gse00001975.apaas.em2.oraclecloud.com/instructional/instructors/disciplines/math?relax=true)

returns records. ICS will be used to resumbit the request with **relax=true** if recordsFound=0.

- Import the Search Library Integration ([SEARCH_LIBRARY_04.00.0000.iar into ICS](https://raw.githubusercontent.com/OracleNATD/vendor-neutral-microservices/master/resources/SEARCH_LIBRARY_04.00.0000.iar)).

	![](https://raw.githubusercontent.com/OracleNATD/vendor-neutral-microservices/master/images/import-integration.PNG)

- Along with the Search Library Integration, the Inbound REST and Mock eCommerce Server Connections were also imported:

	![](https://raw.githubusercontent.com/OracleNATD/vendor-neutral-microservices/master/images/connections.PNG)

- The Connection URL of the Mock eCommerce Service needs to be set the the DNS name of your application container cloud service. For example:

	![](https://raw.githubusercontent.com/OracleNATD/vendor-neutral-microservices/master/images/connection-properties.PNG)

- Test and Save your integration. Select Yes on the Warning that appears:

	![](https://raw.githubusercontent.com/OracleNATD/vendor-neutral-microservices/master/images/ics-warning.PNG)

- Likewise, open the Inbound REST Connection and select Test and Save. 

- Activate the Integration and Enable tracing:

	![](https://raw.githubusercontent.com/OracleNATD/vendor-neutral-microservices/master/images/activate-integration.PNG)

- When Activation is complete, grab the Endpoint URL from the Integration's information icon. We'll need this for the next step:

	![](https://raw.githubusercontent.com/OracleNATD/vendor-neutral-microservices/master/images/endpoint-url.PNG)


### Document the API
In this step [Apiary](https://apiary.io) is used to document the API. Apiary supports both [Swagger](http://swagger.io/) and [API Blueprint](https://apiblueprint.org/). The Library API has been documented using API Blueprint (a nice exercise would be also document the API using Swagger).

- Create a New API Blueprint API called **Library**:

	![](https://raw.githubusercontent.com/OracleNATD/vendor-neutral-microservices/master/images/create-api.PNG)

- Copy and Paste the [Library API Blueprint](https://github.com/OracleNATD/api-platform-sample-instructional/blob/master/apiary.apib) into your new Library API (replacing the sample Polls API) and Save:

	![](https://raw.githubusercontent.com/OracleNATD/vendor-neutral-microservices/master/images/apiary-editor.PNG)


### Manage and Publish the Library API
In this next step we will use the Oracle [API Platform](https://cloud.oracle.com/en_US/api-platform) Cloud Service to manage, publish and monitor the API.

#### Create an API

- Create a new API called **Library**

	![](https://raw.githubusercontent.com/OracleNATD/vendor-neutral-microservices/master/images/create-api.PNG)

- Set the Backend Service URL to the ICS URL we saved from the previous step, excluding the metadata path element at the end:

	![](https://raw.githubusercontent.com/OracleNATD/vendor-neutral-microservices/master/images/backend-service-url.PNG)

- Set the API Endpoint URL to **library**

	![](https://raw.githubusercontent.com/OracleNATD/vendor-neutral-microservices/master/images/api-endpoint-url.PNG)

- Apply a Service Level Auth policy to log us into ICS:

	![](https://raw.githubusercontent.com/OracleNATD/vendor-neutral-microservices/master/images/service-level-auth-policy.PNG)

- Apply an API Rate Limiting Policy of **3** per **Minute**:

	![](https://raw.githubusercontent.com/OracleNATD/vendor-neutral-microservices/master/images/rate-limiting-policy.PNG)

#### Deploy the API
In this step the API is deployed to a gateway.

	![](https://raw.githubusercontent.com/OracleNATD/vendor-neutral-microservices/master/images/deploy-api.PNG)

#### Publish the API 
In this step the API is published to the Developer Portal.

- In the API's Publication tab, name the portal **Library**

- For the Developer Portal API Overview, select **HTML** and then **Link**. Set the link to: https://raw.githubusercontent.com/OracleNATD/vendor-neutral-microservices/master/resources/htmloverview.html.

	![](https://raw.githubusercontent.com/OracleNATD/vendor-neutral-microservices/master/images/developer-portal-api-overview.PNG)

- For Documentation, click the Apiary button, log in and select the Library API:

	![](https://raw.githubusercontent.com/OracleNATD/vendor-neutral-microservices/master/images/select-api-project.PNG)

- Save you Changes and click **Publish to Portal**

	![](https://raw.githubusercontent.com/OracleNATD/vendor-neutral-microservices/master/images/publish-to-portal.PNG)

#### Grant Application Registration Rights
In this step you grant the app-dev-user rights to register applications against this API.

- Switch to the **Grants** tab, select the **Register** tab and click **Add Grantee**. Select a user with an ApplicationDeveloper role:

	![](https://raw.githubusercontent.com/OracleNATD/vendor-neutral-microservices/master/images/add-grantee.PNG)

### Consume the API
Finally we have the front-end [Oracle JET](http://www.oracle.com/webfolder/technetwork/jet/index.html) application which consumes the API (and does a bunch of other stuff).

- Fork the [https://github.com/OracleNATD/OracleJETQuickStart.git](https://github.com/OracleNATD/OracleJETQuickStart.git) to your GitHub account.
- Clone the https://github.com/{your-github-id}/OracleJETQuickStart to your local workstation
- Install the application’s node modules. 
- Open [doc_root\js\viewModels\library.js
](https://github.com/OracleNATD/OracleJETQuickStart/blob/master/doc_root/js/viewModels/library.js) in your favorite editor.
- Update the root variable to point to the address and port of your API Platform gateway load balancer. 
- Start the application:
	```	
	{ OracleJETQuickStart } master » node server.js
	listening on port 8085
	```
- Launch the application from your browser ([http://localhost:8085/?root=library](http://localhost:8085/?root=library)) and search for **Astronomy**:
 
	![](https://raw.githubusercontent.com/OracleNATD/vendor-neutral-microservices/master/images/jet-astronomy.PNG)

- Search for anything else, and ICS will ensure results are still returned:

	![](https://raw.githubusercontent.com/OracleNATD/vendor-neutral-microservices/master/images/jet-other.PNG)

- Search more than 3 times a minute and the API Platform will stop you in your tracks:

	![](https://raw.githubusercontent.com/OracleNATD/vendor-neutral-microservices/master/images/jet-rate-limit.PNG)




 








