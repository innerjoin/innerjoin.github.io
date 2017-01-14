---
layout: post
section-type: post
title: dog2go - Showcase using VSTS for ASP.NET MVC Development
category: tech
tags: [ 'VSTS', 'c#', 'ASP.NET MVC', 'HSR' ]
---

A sophisticated continuous integration setup is a key aspect for successful and efficient team work within a project. As part of our university studies, we have built an online version of a board game. It is known in Switzerland under the name ‘Dog’, Dutch people call it ‘Keezen’ and Canadians should be familiar with it being named ‘Tock’. The Backend is coded in [ASP.NET MVC](https://www.asp.net/mvc), the frontend visualization is using the [Phaser gaming library](https://phaser.io/) and the communication is done over WebSockets, in our case provided by [SignalR](https://signalr.net/). It took me quite a while to get it up and running, so let me guide you through the – in same corner – tricky setup.

## What is VSTS ?

As with every project, it started by evaluating a good platform which covers our needs to track & plan our work, manage our projects source code and continuously integrate it. We decided to go with Visual Studio Team Services – the hosted version of Microsoft Team Foundation Server – for the following reasons:
<ol>
  <li> No setup and hardware infrastructure required</li>
  <li> Built-in support for source code management with git, basic track & plan features, build automation, automated testing and deployment all on the same platform</li>
  <li> Free for up to five users including 240 minutes Build on a hosted build agent</li>
</ol>
  
## VSTS Setup
Head over to [visualstudio.com](https://www.visualstudio.com) and search for the paragraph ‘Visual Studio Team Services - Get Started for Free’. Next, you need to sign-in with a Microsoft account. After that, you are asked to provide a project name used to create your own subdomain. Your VSTS instance will then be available under the URL [https://YOUR-PROJECT.visualstudio.com](). Finally, you can create your first project. The setup is incredibly easy, so I won’t bore your with it.

## Create the MVC Project
Head over to Visual Studio, I assume that you have basic knowledge of it. Use the <code> File -> New -> Project… </code> wizard to create a new ‘ASP.NET Web Application’. We are using the ‘MVC’ template and do not host the application in the cloud. Make sure that you check ‘Add to source control’ and ‘Create directory for solution’. You will probably get asked about whether you want to use Git or TFS as your source control system. We will be going with Git. Open the Team Explorer and setup a new connection to your above created VSTS server. The steps following should be easy as well. Finally, commit and push your locally created project to the remote branch on VSTS. You can find plenty of documentation on how to do this in the web. 

## Track & Plan in VSTS
We have used the Track and Plan features of VSTS. It is possible to customize the work item attributes for a specific work item type. Coming from [RTC Jazz](https://jazz.net/rational-team-concert), I missed a lot of configuration possiblities and many features which I liked there, but I must admit that for a small team the features provided by VSTS were sufficient for our work.
![Nuget Restore](/img/posts/vsts-plan.PNG)

## Build Automation Setup
Now let's talk a little about the tricky part. We had to achieve the following goals for our CI process:
<ol>
  <li>Install Required NuGet Packages </li>
  <li>Build the ASP.NET MVC Application </li>
  <li>Run the NUnit and Jasmine Tests </li>
  <li>Publish Artifact for Download </li>
</ol>

### NuGet restore
Simply reference the desired solution for which a NuGet package restore should executed and you are in!
![Nuget Restore](/img/posts/dog2go-build-1.PNG)

### Build the Application
The next step is to build the application. Reference the desired project file (in our case we had only one project to build), and provid the appropriate MSBuild arguments:

<code>/p:Platform=AnyCPU;Configuration=Release;PublishDestination=Deployment /t:PublishToFileSystem</code>

It's important to mention that in this case the output will written to the <code>Deployment</code> directory and that our build uses the <code>PublishToFileSystem</code> option, which allowed as to publish the build result directly via FTP onto our development server.

![Build Solution](/img/posts/dog2go-build-2.PNG)

### Test the Code
I don't have to tell you why Test Automation is important. I can only tell you that we reached a coverage of 55.5%, which is really poor... But to come back to the topic, it is important to define in which directories the test files are stored. Additionally, if you are using an external test adapter as we do for running our Jasmine tests, reference it in <code>Path to Custom Test Adapters</code>
![Test Assemblies](/img/posts/dog2go-build-3.PNG)

### Publish
The last step we did was to publish our build results for Download, like this:
![Copy Publish Artifact](/img/posts/dog2go-build-4.PNG)

## Project Source
The information provided in this post might not be sufficient enough for you to get started with the project, so head over to my [Github](https://github.com/innerjoin) profile where I published [the entire source](https://github.com/innerjoin/dog2go) of our dog2go game.