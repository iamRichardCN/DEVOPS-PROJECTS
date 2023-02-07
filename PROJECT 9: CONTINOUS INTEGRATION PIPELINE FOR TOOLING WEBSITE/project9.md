 # PROJECT 9: CONTINOUS INTEGRATION PIPELINE FOR TOOLING WEBSITE
 
In previous Project 8 i employed horizontal scalability concept to add new Web Servers to our Tooling Website. if they are just 2 or three servers, tt is not a big deal to configure them manually... Imagine that you would need to repeat the same task over and over again adding dozens or even hundreds of server.

In this project i will be automating part of our routine tasks with a free and open source automation server – Jenkins. It is one of the most popular CI/CD tools, it was created by a former Sun Microsystems developer Kohsuke Kawaguchi and the project originally had a named "Hudson

In this project i will be utilizing Jenkins CI capabilities to make sure that every change made to the source code in GitHub [tooling](https://github.com/iamRichardCN/tooling) will be automatically be updated to the Tooling Website.

Here is how the architecture will look like upon competion of this project:

![image](https://user-images.githubusercontent.com/111741533/217116655-c7af1ec1-611c-49bb-a428-a99ff9862caa.png)

## INSTALLING  AND CONFIGURING THE JENKINS SERVER

### step 1: Step 1 – Install Jenkins server

- Create an AWS EC2 server based on Ubuntu Server 20.04 LTS and name it "Jenkins"

- Install JDK (since Jenkins is a Java-based application)
```
sudo apt update
sudo apt install default-jdk-headless
```

-Install Jenkins

```
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
    /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt-get install jenkins
```


<img width="958" alt="image" src="https://user-images.githubusercontent.com/111741533/217117470-7451805a-8e81-4489-be8a-65838e7ac9a0.png">


- By default Jenkins server uses TCP port 8080 – open it by creating a new Inbound Rule in your EC2 Security Group

<img width="821" alt="image" src="https://user-images.githubusercontent.com/111741533/217117673-294a57c9-2212-46c8-ad25-456744155643.png">

- Perform initial Jenkins setup, on Jenkins web (remember to use ```sudo cat /var/lib/jenkins/secrets/initialAdminPassword``` to generate the password for initial login)
- 
<img width="916" alt="unlock jenkins" src="https://user-images.githubusercontent.com/111741533/217119184-857ff7e2-ed6c-4aa5-8c61-15e6f56f042c.png">


![image](https://user-images.githubusercontent.com/111741533/217117971-6d8d8559-4dbe-4a9e-b85b-7efca2dca8d5.png)

(**select install suggested plugin**)

### Step 2 – Configure Jenkins to retrieve source codes from GitHub using Webhooks

-Enable webhooks in your GitHub repository settings

<img width="959" alt="image" src="https://user-images.githubusercontent.com/111741533/217118299-6bc20766-2c8e-42f2-91da-f7defa4953e2.png">


- Go to Jenkins web console, click "New Item" and create a "Freestyle project"

<img width="932" alt="image" src="https://user-images.githubusercontent.com/111741533/217118500-cfce5cf7-2970-40cc-8902-f10eebfd9adc.png">


To connect the GitHub repository, enter the link to the repositoy to the source code management, enter your credentials and save 


<img width="662" alt="image" src="https://user-images.githubusercontent.com/111741533/217118746-daa41dea-5ba6-41a6-a70a-2d278f09a720.png">



Now, go ahead and make some change in any file in your GitHub repository (e.g. README.MD file) and push the changes to the master branch.

You will see that a new build has been launched automatically (by webhook) and you can see its results – artifacts, saved on Jenkins server.


<img width="952" alt="image" src="https://user-images.githubusercontent.com/111741533/217119084-04c071ea-473c-43e7-a7cf-ba3425d11399.png">


- use  ```ls /var/lib/jenkins/jobs/tooling_github/builds/<build_number>/archive/``` to see uild history from terminal

<img width="505" alt="image" src="https://user-images.githubusercontent.com/111741533/217119435-a0cc2476-5e90-4965-89e7-5e6bc0a2740c.png">


### CONFIGURE JENKINS TO COPY FILES TO NFS SERVER VIA SSH

- Install "Publish Over SSH" plugin.

<img width="894" alt="publish over ssh download" src="https://user-images.githubusercontent.com/111741533/217119541-27785b50-25d4-4ccd-b562-102551e52ffa.png">

- Configure the job/project to copy artifacts over to NFS server.
- 
Test the configuration and make sure the connection returns Success. Remember, that TCP port 22 on NFS server must be open to receive SSH connections.

<img width="842" alt="test configuration in jenkins" src="https://user-images.githubusercontent.com/111741533/217119635-5f993ace-214f-4172-9563-93c32a9bee93.png">

- Save the configuration, then add 'send build artifact over ssh' as  another "Post-build Action"
- Configure it to send all files probuced by the build into our previouslys define remote directory. In our case we want to copy all files and directories – so we use **.
- Save this configuration and change something in README.MD file in of the GitHub Tooling repository.


<img width="944" alt="console output" src="https://user-images.githubusercontent.com/111741533/217119940-a70810ba-49f1-4bdf-a579-0ef9894a20bc.png">

- confirm the change in the terminal using ```cat /mnt/apps/README.md```


<img width="614" alt="terminal final" src="https://user-images.githubusercontent.com/111741533/217119977-833c0e17-4071-4c44-aaf4-4bb1b896f2d4.png">











