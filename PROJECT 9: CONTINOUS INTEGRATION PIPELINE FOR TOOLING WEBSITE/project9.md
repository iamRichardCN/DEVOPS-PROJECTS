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








