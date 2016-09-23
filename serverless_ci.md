#  Serverless CI

### What is Hyper_?
Hyper_ is a container-native cloud service. Different from traditional IaaS and CaaS, Hyper_ does not employ VMs (cluster). Container is the only object the users work with. Hyper_’s secret source is its container runtime technology, which combines the VM-level isolation and the speed and immutability of container, making using and deploying containers to production as simple as using Docker on your laptop. 

### Why run Jenkins in Hyper_?
Jenkins is the market leader for CI/CD workloads. The two most popular plugins are currently for AWS and DigitalOcean. Jenkins Amazon EC2 Plugin allows user to provision EC2 instances as Jenkins Slave Node, while DigitalOcean Plugin allows the user to provision Droplet as Jenkins Slave Node. Both are VM focused solutions. 

The Hyper_ plugin allows users to launch containers on demand in Hyper_ as Jenkins slave node. This presents a number of benefits:
- Lower Cost: per second billing (hourly pricing in AWS and DO)
- Faster to start: 3~10 seconds (55s in DO)
- Compatibility: no changes to workflow `TBD`
- Easy to use: Designed for Docker

### Getting Started
#### Sign Up
You can follow the instructions to setup your account:
- [Create an account](https://console.hyper.sh/register)
- [Install `hyper` CLI on your laptop](./install.md)
- [Generate a credential and configure the CLI](generate_api_credential.md)

#### Deploy Jenkins Server

Firstly, we need to deploy the Jenkins server. And that only takes two commands in Hyper_:

``` bash
// launch a jenkins server container
$ hyper run -d --name jenkins-server --size=m1 -P hyperhq/hyperkins

// attach a public ip to the container
$ hyper fip attach $(hyper fip allocate 1) jenkins-server
```

### Configure Jenkins
Once the server is launched, it generates a random initial password for the Jenkins admin account. To find out the password, run:

``` bash
$ hyper exec -it jenkins-server cat /var/jenkins_home/secrets/initialAdminPassword
```

Then open the Jenkins web portal in the browser to access Jenkins Master Web UI, enter the initial password to unlock the system:

![](https://trello-attachments.s3.amazonaws.com/57d684a58b3ece1c9e0e8b10/609x253/3d0642c8bf454f4fd121b394043b968b/upload_9_12_2016_at_6_35_59_PM.png)

Next, let's configure the Hyper_ credential to allow Jenkins to launch slave containers:

- Login the web portal;
- goto menu `Manage Jenkins` -> `Configure System`
- Enter Hyper_ `Access Key` and `Secret Key`
- Click `Save credential`
- Click `Test connection to Hyper_`
![](https://trello-attachments.s3.amazonaws.com/57d684a58b3ece1c9e0e8b10/609x271/ce114c09eeca2b1733e55709c02f24c0/upload_9_12_2016_at_6_37_47_PM.png)

Also, Jenkins server need to pass its URL to the slave containers, in order for the slave containers to connect. To find out the public IP of the Jenkins server:

``` bash
$ hyper inspect --format '{{.NetworkSettings.IPAddress}}' jenkins-server
172.16.1.200
```

- Login the web portal
- goto menu `Manage Jenkins` -> `Configure System` -> `Jenkins Location`
- `Jenkins URL`: http://172.16.1.200:8080/

![](https://trello-attachments.s3.amazonaws.com/57d684a58b3ece1c9e0e8b10/580x89/63fb5effe8c57ff346475162854ebacc/upload_9_12_2016_at_6_39_14_PM.png)

Enable JNLP
- (Menu)Manage Jenkins 
- Configure Global Security:

### Submit Jobs
Check “Run the build inside Hyper_ Container”

![](https://trello-attachments.s3.amazonaws.com/57d684a58b3ece1c9e0e8b10/609x275/8dc840d49cfee9354d8f81dc2f8c7fc6/upload_9_12_2016_at_6_41_48_PM.png)

Run Jenkins Job in Hyper_ Slave

![](https://trello-attachments.s3.amazonaws.com/57d684a58b3ece1c9e0e8b10/624x264/fe8e61e15ab2b4bc0a14609716008342/upload_9_12_2016_at_6_42_00_PM.png)

That’s all.