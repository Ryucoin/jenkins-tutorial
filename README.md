# Jenkins Tutorial

[Jenkins](https://en.wikipedia.org/wiki/Jenkins_(software)) is an open source automation server written in Java. It is widely used to build and test projects continuously. This tutorial shows how to use Jenkins CI with a Github repository using [webhooks](https://en.wikipedia.org/wiki/Webhook).

## Continuous Integration?

In software engineering, continuous integration (CI) is the practice of merging all developer working copies to a shared mainline several times a day [source](https://en.wikipedia.org/wiki/Continuous_integration).

A CI server checks every time code is updated to see if it builds and passes all of its tests. It ensures that new code being merged into a project does not break the system.

## How does it work?

1. Developers push changes to their code.
2. Github POSTs a notification to the Jenkins server of the change.
3. Jenkins receives the notification via webhooks.  
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
