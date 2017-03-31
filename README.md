# Developing, Integrating and Protecting Vendor Neutral Microservices Demo Script #

This script demonstrated how a microservice can be developed using [Twelve-Factor](https://12factor.net/) principals, integrated using Oracle Integration Cloud Service and protected using the Oracle API Platform.

## Pre-requisites ##
You have the following cloud accounts:

- Oracle [Application Container](https://cloud.oracle.com/en_US/application-container-cloud) Cloud Service
- Oracle [Integration](https://cloud.oracle.com/en_US/integration) Cloud Service
- Oracle [API Platform](https://cloud.oracle.com/en_US/api-platform) Cloud Service
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