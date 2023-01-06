# DEVOPS TOOLING WEBSITE SOLUTION

In the previous project i implemented a WordPress based solution that is ready to be filled with content and can be used as a full fledged website or blog. 

**Project 6** (https://github.com/iamRichardCN/DEVOPS-PROJECTS/blob/main/project6%20WEB%20SOLUTION%20WITH%20WORDPRESS.md)] 

Moving further, in this project i will be implementing a tooling website solution which makes access to DevOps tools within the corporate infrastructure easily accessible. The solution implemented will consists of following components:

**Infrastructure**: AWS
**Webserver Linux**: Red Hat Enterprise Linux 8
**Database Server**: Ubuntu 20.04 + MySQL
**Storage Server**: Red Hat Enterprise Linux 8 + NFS Server
**Programming Language:** PHP
**Code Repository:** GitHub

## Architectural Design

![image](https://user-images.githubusercontent.com/111741533/211112407-ef4706bd-6fb2-411e-a598-0cba7f015658.png)

On the diagram above you can see a common pattern where several stateless Web Servers share a common database and also access the same files using Network File Sytem (NFS) as a shared file storage. Even though the NFS server might be located on a completely separate hardware – for Web Servers it look like a local file system from where they can serve the same files.

## Preparing NFS Server

Created an EC2 instance (Red Hat Enterprise Linux 8 on AWS) on which we will setup our NFS(Network File Storage) Server.
On this server we attach three EBS volumes 10GB each as external storage to our instance and create 3 logical volumes on it through which we will attach mounts from our external web servers.

3 logical volumes lv-opt, lv-apps and lv-logs
3 mount directory /mnt/opt, /mnt/apps and /mnt/logs
Webserver content will be stores in /apps, webserver logs in /logs and /opt will be used by Jenkins







