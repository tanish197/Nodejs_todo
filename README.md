## IN THIS PROJECT WE ARE GOING TO USE JENKINS CI/CD WITH GITHUB INTEGRATION AND AND DOCKER FOR CONTAINERIZATION WITH JENKINS AND AWS

### STEP 1: Setup environment
   1. Launch AWS EC2 instance 
   2. Give a name and Select Ubuntu as a server.
   3. Check HTTPS connection under security because it is related to https.
   4. Click Launch
   5. Connect to EC2 server
   ### Now run the following commands for further process:
       • sudo apt update
       // now will install java because jenkins in based upon java.
       • sudo apt install openjdk-11-jre
       • java -version
       • curl -fsSL https://pkg.jenkins.io/debian/jenkins.io.key | sudo tee \   /usr/share/keyrings/jenkins-keyring.asc > /dev/null 
       • echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \   https://pkg.jenkins.io/debian binary/ | sudo tee \   /etc/apt/sources.list.d/jenkins.list > /dev/null
       • sudo apt-get update 
       // if you face an error then follow these commands: 
            • wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
            • sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
        // now again run the command:
        • sudo apt-get update
        • sudo apt-get install jenkins
	    • sudo systemctl enable jenkins
	    • sudo systemctl start jenkins
	    • sudo systemctl status jenkins
    // Now go to EC2 instance and add a security inbound in security group: 
    Type: Custom TCP 
    port no:  8080
    Source: MyIP ( because no other can access to this IP address) 
        • Now copy the public IPv4 of instance and browse it as for e.g.: 65.1.109.53:8080 
        // This will open Jenkins Welcome Page. Now, get the password and use it from using this command:
	    • sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   6. Complete the Jenkins Login process.
   7. Now create a job. Give it a name and choose a freestyle and click OK
   // Note; you should make a git repository either your project or cloned one anything doesn't matter.
   8. Write your Description and choose Github project(because we have to pull it from git) and provide the repository link.
   9. Choose the git and provide the same link of repo now we have to add credentials
   ### Follow these steps before adding credentials:
     • Go to EC2 server terminal and write ssh-keygen
     • It will create the public and private key
     • Enter cd .ssh
     • cat id_rsa.pub (for public key)
     • cat id_rsa (for private key)
     • COPY BOTH
     • Now go to Github and open settings and click on SSH key generation. Create new key and give a name and paste the public key there and save.
     • Now get back to jenkins page and click on add credentials and choose SSh key with private key and give a name and enter the username which is "ubuntu' in this case and paste the private key and save.
     • Now choose the credential made.
     • edit the branch as per your repository. By default is */master
     • Click apply and save.
     • Now the build the job and check if it success. It should be successful if done no mistake.(Refer to SS:img 14)
  ### Check if Jenkins and Github are integrated or not:
        • cd /var/lib/jenkins/workspace/nodejs-todo 
        • ls
        And you can see the files here now of git repository.
        
### Now we have to run the application

### Run these commands:
    • sudo apt install nodejs
    • sudo apt install npm
    • sudo npm install
    • node app.js
    // Add 8000 to inbound connection:
    Type: Custom TCP 
    port no:  8000
    Source: IPv4 ( because  other can access to this IP address)
    • copy the IPv4 of EC2 instance and search to open the application, for e/g. 65.1.109.53:8000


 ### But it will stop when we do Ctrl+C.
 ### So we will use Docker to containerize it   
 
### Open EC2 terminal and follow these commands:
    • sudo apt install docker.io
    • docker build . -t node-app
    • sudo usermod -a -G docker $USER
    • docker run -d --name node-app -p 8000:8000 todo-node-app

### Now it will run with Docker

## Auto buildiing Docker File through jenkins
 ### Steps:
    • Go to jenkins job and open COnfigure
    • Write build steps
    • docker build . -t node-app
    • docker run -d --name node-app -p 8000:8000 todo-node-app
    • Apply and save.
    • Go to EC2 terminal and use these commands before clicking on build now.
    • sudo chmod 777 /var/lib/jenkins/workspace/nodejs-todo
    • sudo usermod -a -G docker jenkins
    • sudo systemctl restart jenkins
### use below command if it gives error after building
    • docker kill *image_name*
    • docker rm *image_name*

### It will successfully runn

### Using Continous Integration using webhooks
    • Go to Github repository settings and go on webhooks. Enter the source as of jenkins i.e., 65.1.109.53:8080 and also don't forget to add github-webhook/ at the end. Overall write like http://65.1.109.53:8080/github-webhook/
    • Before doing okay. Change the inbound of 8080 to Anywhere from MyIp so that github can access it.
    • Now click Ok
    • Now go to jenkins jobs and enable Github hook trigger for Gitscm polling under build triggers and save.
    • Now try make changes in files on github code.
    • Now it will successfully Build without clicking on build now.
  # Boom , We did it ;)

