# Microprofile Appsody Codewind Quicklab

Moving workloads to the cloud introduces a steep learning curve for developers accustomed to traditional web application developmenr. Cloud-native applications are usually containerized (either Docker or OCI containers) and are run on orchestrated platforms like Kubernetes. These are complex technologies that bring big changes to the development workflow. Can this be simplified? Can developers create cloud-native applicatioons and deploy them to Kubernetes without climbing the mountainous learning curve? 

In this quicklab we will take a look at a Appsody and Codewind, two technologies that simplify development of containerized cloud-native appplications. Appsody provides self-contained stacks for creating, building, and deploying cloud-native applications in a consistent and customized way for teams and organizations. Eclipse Codewind is an IDE plugin that allows you to work with containerized applications in a familiar way.

Appsody and Codewind allow developers to start building cloud-native applications like an expert without having to be an expert in all the cloud-native technologies. 

## Prerequistes

If you're running this lab on your own laptop, we need to pre-install some tools.

<details>
  <summary>Click to expand</summary>
  
### Configure Local System

This quicklab requires the following tools: 

1. Install Docker
2. Install minikube
3. Install Appsody
4. Install VS Code
5. 	Install VS Code codewind extension
6. Install VS Code java extension

We recommend working with the latest available version of each.

</details>

## Getting Familiar with Appsody

[Appsody](https://appsody.dev/) is a command-line interface for creating, building, and deploying cloud-native projects. Let's look at how to use Appsody to initialize a Microprofile project. 

Appsody uses a concept called "stacks". The appsody CLI defines a few simple commands that work with defined stacks to create, run, test, debug, build, and deploy applications to Kubernetes. To view available stacks run the following command:

```
$ appsody list
```

This prints out a list of available stacks including the repo they are being pulled from, their version, and the different types of templates. 

```
REPO        	ID                       	VERSION  	TEMPLATES        	DESCRIPTION                                              
appsodyhub  	java-microprofile        	0.2.12   	*default         	Eclipse MicroProfile on Open Liberty & OpenJ9 using Maven
appsodyhub  	java-spring-boot2        	0.3.10   	*default, kotlin 	Spring Boot using OpenJ9 and Maven                       
appsodyhub  	nodejs                   	0.2.5    	*simple          	Runtime for Node.js applications                         
appsodyhub  	nodejs-express           	0.2.5    	*simple, skaffold	Express web framework for Node.js                        
appsodyhub  	nodejs-loopback          	0.1.4    	*scaffold        	LoopBack 4 API Framework for Node.js                     
appsodyhub  	python-flask             	0.1.3    	*simple          	Flask web Framework for Python                           
appsodyhub  	swift                    	0.1.4    	*simple          	Runtime for Swift applications                           
experimental	java-spring-boot2-liberty	0.1.7    	*default         	Spring Boot on Open Liberty & OpenJ9 using Maven         
experimental	nodejs-functions         	0.1.3    	*simple          	Serverless runtime for Node.js functions                 
experimental	quarkus                  	0.1.5    	*default         	Quarkus runtime for running Java applications            
experimental	vertx                    	0.1.1    	*default         	Eclipse Vert.x runtime for running Java applications   
```

Organizations will have their own specific needs and requirements. For which custom stacks can be created, which can be built of existing stacks, or fully created new. For more information on creating custom stacks you can read this article on [IBM Developer](https://developer.ibm.com/articles/customizing-appsody/).
 
## Improving Developer Productivity with Eclipse Codewind

[Eclipse Codewind](https://www.eclipse.org/codewind/) is a plugin for IDEs, currently available in VS Code, Eclipse, and Eclipse Che, that helps improve developer productivity when developing containerized applications. Let's explore how Codewind can help you be a more productive developer.

1. Open VS Code (press **command** + **space bar** and type "VS Code" into the dialog box)
2. In the explorer window under **CODEWIND** click on the "**+**" to create a new project
	![](images/codewind-explorer.png)
3. In the dialog pop-up search for "Microprofile template" and select the "Appsody Microprofile template" option	![](images/codewind-new-project.png)
4. Enter **cloud-native-microprofile** as the project name and hit enter
	![](images/codewind-new-project-part2.png)

### Automated Code Reload

A key to increasing developer productivity is shortening and reducing the friction in the feedback loop. `appsody run` will run the Microprofile application in the foreground, restarting the application in the container when changes have been detected. Codewind brings this appsody capability into the IDE in a familiar way.  

Let's look at this feature in action.

3. In VS Code add the **cloud-native-microprofile** project folder to the explorer
	![](images/add-folder.png)
4. 	In the project under **src/main/java/dev/appsody/starter** create a new file **Hello.java**
5. Edit **Hello.java** to look like below:
	
	```java
	package dev.appsody.starter;
	
	import javax.ws.rs.GET;
	import javax.ws.rs.Path;
	import javax.ws.rs.QueryParam;
	
	@Path("/v1/hello")
	public class Hello {
	
	    @GET
	    public String getRequest(@QueryParam("name") String name) {
	        if(name == null){
	            return "Hello World";
	        } else {
	            return "Hello " + name;         
	        }
	    }
	}
	```
6. You can view the status of the re-build and re-deploy by looking at the status indictaor next to the project under the Codewind context. Once status returnss to [Running][Build Suceeded] you can refresh your browser window to view the change we made. 
	![](images/project-status.png)	
1. In VS Code click the "go to application" icon	![](images/open-project.png)
2. Append `/starter/v1/hello?name=EclipseCon` to the end of the url

### Viewing Application Logs

Viewing the logs of your application running in a docker container is easy from the IDE with Codewind. 

To view the logs for an application, right click on it and select "Show All Logs" 

![](images/show-logs.png)

The logs for the running application will be shown in the IDE console log window on the bottom right of the page. 


### Container Command Line Access

Codewind also provide easy access to the command line of a container within your IDE as well. This can allow you to explore your container making sure all the files are where they are supposed to be located along with the correct values, or any other administrative needs you might have. 

To view the terminal for a container, right click on it and select "Open Container Shell" 

![](images/container-shell.png)

The terminal for the running container will be shown in the IDE console window on the bottom right of the page. 

![](images/terminal.png)

### Deploying an Application to Kubernetes with Appsody 

The cloud native world demands developer learn a lot of new skills that traditionally they didn't need understand before. Appsody helps to reduce this learning curve by helping with tasks like deploying to a kubernetes cluster. Let's deploy the application we have been building in this quicklab to a local kubernetes cluster!

1. First we will need to start our minikube cluster:

	```
	minikube start
	```

1. Next we will need to setup docker registry for the local minikube cluster, so that minikube can pull the image we will be sending it in the next step. To do that run the following command:

	```
	eval $(minikube docker-env)
	```

1. To deploy the application we just created to a kubernetes cluster, in this case a locally running instance of [minikube](https://github.com/kubernetes/minikube), run the following command:

	```
	appsody deploy
	``` 

1. Once the deploy has completed, we will need to tell minikube to expose the service, to do this run the following command:

	```
	minikube service cloud-native-microprofile
	```

Minikube will expose the service and open a browser window allowing you to view the application we just deployed.

Appsody and Codewind help Java developers create, build, and deploy cloud-native applications without having to be experts in cloud native development!

