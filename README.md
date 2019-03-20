# Jenkins Tutorial

This tutorial was written for use with macOS 10.13.6 (High Sierra).

[Jenkins](https://en.wikipedia.org/wiki/Jenkins_(software)) is an open source automation server written in Java. It is widely used to build and test projects continuously. This tutorial shows how to use Jenkins CI with a Github repository using [webhooks](https://en.wikipedia.org/wiki/Webhook).

---

## Continuous Integration?

In software engineering, continuous integration (CI) is the practice of merging all developer working copies to a shared mainline several times a day [source](https://en.wikipedia.org/wiki/Continuous_integration).

A CI server checks every time code is updated to see if it builds and passes all of its tests. It ensures that new code being merged into a project does not break the system.

---

## How does it work?

1. Developers push changes to their code.
2. Github POSTs a notification to the Jenkins server of the change.
3. Jenkins receives the notification via webhooks.  
4. If project build or test fails CI server sends notifications to team (e.g. by e-mail).
5. CI server generates reports.

---

## Setup Jenkins

### Download Jenkins

First, you will need to install prerequisites for testing on macOS (you may not need or require all of these).

1) If you have not already installed [Homebrew](https://brew.sh) do that first:

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
2) Install Cocoa Pods:

```
sudo gem install cocoapods
```

3) Install Java with Homebrew:

```
brew cask install homebrew/cask-versions/java8
```

Next, go ahead and install Jenkins via Homebrew:

```
brew install jenkins
```

Start Jenkins (and automatically restart it when logging in)

```
brew services start jenkins
```

### Setup Jenkins

Once Jenkins is running go to ```localhost:8080```. Jenkins will begin the setup process and then you will need to manually do some setup.

1) Unlock Jenkins by following the prompt (copy the value at `/Users/mac/.jenkins/secrets/initialAdminPassword`).
2) Select which packages you need. Use the default packages and `Github`.
3) Create an admin account
4) Click `Save & Finish` and `Start Using Jenkins`

---

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
