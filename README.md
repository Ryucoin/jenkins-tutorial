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

```
./startup.sh
```

3. Now go to your **[localhost:8080/jenkins](localhost:8080/jenkins)** in your browser. Jenkins is running!
4. to stop tomcat, type  

```
 ./shutdown.sh
```

### Use Jenkins in built in Jetty servlet
Jenkins has built in **Jetty** servlet container. **cd** (change directory in terminal) into to your jenkins.war folder and type:

```
java -jar jenkins.war
```

Now go to your **[localhost:8080](localhost:8080/jenkins)** in your browser. Jenkins is running!

## Communication between Jenkins and Github
We need to communicate between our Jenkins Server and Github Repository. Since we are working on localhost, we need to make it available for Github. We will use [serveo.net](serveo.net) for this.

Serveo does not require any download, installation or signup for use. It simply uses ssh for tunneling.

You can simply run:
```
ssh -R 80:localhost:8080 serveo.net
```

Serveo will then assign you a url like `https://abc.serveo.net`.

However, you won't be guaranteed to always have the same domain:

> The subdomain is chosen deterministically based on your IP address, the provided SSH username, and subdomain availability, so you'll often get the same subdomain between restarts. You can also request a particular subdomain:

### Request a subdomain

You can [request a specific Serveo domain](https://security.stackexchange.com/a/184951) (out of the 3 to 5 thousand available):

```
ssh -R magis:80:localhost:8080 serveo.net
```

Where `magis` is a specific subdomain.

However, note:

> If somebody else has taken it when you try to connect, you'll get a different subdomain. [source](https://security.stackexchange.com/questions/184829/any-alternative-to-ngrok-for-constant-connection#comment362569_184951)

### Use a custom domain

So your safest bet is to use a custom domain. The full instructions are on [the serveo website](http://serveo.net), but can be summarized in these three steps:

1) `ssh-keygen -l` and note your key's fingerprint
2) Add an A record pointing to `159.89.214.31`
3) Add a TXT record `authkeyfp=[fingerprint]`

Then you can establish the connection with:

```
ssh -R subdomain.example.com:80:localhost:8080 serveo.net
```

### Keep Serveo connection alive

You can use `autossh` to keep the tunnel alive. On macOS you install it with Homebrew:

```
brew install autossh
```

And then use `autossh` to connect:

```
autossh -M 0 -o "ServerAliveInterval 30" -o "ServerAliveCountMax 3" -R subdomain.example.com:80:localhost:8080 serveo.net
```

### Github setup

- Go to your Github repository. Click on Settings -> Webhooks & services.
- Click on **Add service** dropdown. Select **Jenkins (Git plugin)**.
- Paste your webhook link here. It should look like **https://subdomain.example.com/jenkins/github-webhook/**
- Click "Add service" and "Test service".

## Resources
1. [Jenkins merges branches into master](https://www.cloudbees.com/blog/dont-phunk-my-stable-branch-jenkins-pre-tested-commits-stop-breaking-stable-branches )
2. [Jenkins vide tutorial series](https://www.youtube.com/watch?v=1JSOGJQAhtE)
3. [Jenkins step by step tutorials](http://www.tutorialspoint.com/jenkins/index.htm)
4. [Serveo](http://serveo.net)
5. [autossh](https://www.everythingcli.org/ssh-tunnelling-for-fun-and-profit-autossh/)
