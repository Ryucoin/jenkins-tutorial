# Jenkins Tutorial
Jenkins is a continuous integration(CI) software. It is widely used for build and test projects continuously and integrate the changes. It is center of the builds for a developer team. In this tutorial we are going to use Jenkins CI with Github repository using Webhooks to notify Jenkins server. Also we will show you how to set up Jenkins locally using **Tomcat** and how to tunnel your localhost using **ngrok** so Github Webhooks can interact with your localhost like a server and push changes to the Jenkins server.

## Continuous Integration?
We have a developer team. They each code a few new classes and tested them well. But when they integrate their classes together bugs arise and code breaks. This problem is called an **integration hell**. Realizing this problem may be too late if the project schedule is near to the end as well as it will be expensive. To solve this problem, people get inspired from eXtreme Programming(XP).
The idea is simple. Instead of waiting lost of components to integrate, project is build whenever a developer pushes a new change to repository. So it will automatically build test and report each changes.

## How it actually works?
1. Developers push new code.
2. Repository (Github in this tutorial) changes.
3. CI server (local Jenkins in this tutorial) get notification either by Poll or Webhooks.  
  **Poll**  
  CI server checks the repository regularly. It scans entire repository and verify it with the server. It is more expensive method than webhooks.  
  **Webhooks**
  > Webhooks allow external services to be notified when certain events happen within your repository.

  is defined by Github Webhooks. When the repository changes, Github will POST the changes to our serever by adding server link to our Github settings. This will be explained soon in this tutorial.
4. If project build or test fails CI server sends notifications to team (e.g. by e-mail).
5. CI server generates reports.

### Download Tomcat
*If you don't want to host your jenkins in Tomcat, skip those steps and keep reading [here](#use-jenkins-in-built-in-jetty-servlet)*

1. I have used latest tomcat version for now which is 9. Go [here](http://tomcat.apache.org/download-90.cgi) chose **Core** and select appropriate version for your system, i have chose **tar.gz** for my **OS X EL Capitan**
2. Extract it to somewhere that you have access, my tomcat location now is **/Users/cemalonder/Development/Libraries/apache-tomcat-9.0.0.M8**

### Download Jenkins
1. Go [here](https://jenkins.io/) and click on **Downlads** dropdown on the upper left, choose **2.9.war** file (or latest version at the time you are reading this tutorial).
2. Move **jenkins.war** file **webapps** folder which is under the tomcat path in the previous step. So my jenkins location is **/Users/cemalonder/Development/Libraries/apache-tomcat-9.0.0.M8/webapps/jenkins.war**

### Startup server
1. **cd** into your tomcat/bin path. Mine is **/Users/cemalonder/Development/Libraries/apache-tomcat-9.0.0.M8/bin**
2. type  

 > ./startup.sh

3. Now go to your **[localhost:8080/jenkins](localhost:8080/jenkins)** in your browser. Jenkins is running!
4. to stop tomcat, type  

 > ./shutdown.sh


### Use Jenkins in built in Jetty servlet
Jenkins has built in **Jetty** servlet container. **cd** (change directory in terminal) into to your jenkins.war folder and type:

  > java -jar jenkins.war

Now go to your **[localhost:8080](localhost:8080/jenkins)** in your browser. Jenkins is running!

## Communication between Jenkins and Github
We need to communication between our Jenkins Server and Github Repository. Since we are working on localhost, we should tunnel localhost to make it available for Github. We will use **ngrok** for this purpose.
- downlad ngrok matches with your system [here](https://ngrok.com/download)
- unzip it. My **ngrok** location is **/Users/cemalonder/Development/Libraries** cd into here.
- type:

  > ./ngrok http 8080

- ngrok terminal will pop up, copy the href next to the **Forwarding**

  >  Forwarding                    http://21db9b29.ngrok.io -> localhost:8080

  It is http://21db9b29.ngrok.io in my terminal. Now it is our localhost:8080 which is the port tomcat runs. When someone clicks on this link while we keep ngrok terminal running, they can get our localhost like getting a webpage!

- Go to your Github repository. Click on Settings -> Webhooks & services. In my case
**https://github.com/ciglipaf/jenkins-tutorial/settings/hooks** it is.
- Click on **Add service** dropdown. Select **Jenkins (Git plugin)**.
- Paste your ngrok link here with some addition. Our ngrok link hrefs to tomcat, while we want to go jenkins, add "/jenkins" and "/github-webhook/" for the webhook part. Final link is **http://21db9b29.ngrok.io/jenkins/github-webhook/**
- Click "Add service" and "Test service".

## Resources
1. [Jenkins merges branches into master](https://www.cloudbees.com/blog/dont-phunk-my-stable-branch-jenkins-pre-tested-commits-stop-breaking-stable-branches )
2. [Jenkins vide tutorial series](https://www.youtube.com/watch?v=1JSOGJQAhtE)
3. [ngrok tutorial](https://www.sitepoint.com/accessing-localhost-from-anywhere/)
4. [Jenkins step by step tutorials](http://www.tutorialspoint.com/jenkins/index.htm)
