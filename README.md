# Getting Started with Create React App

This project was bootstrapped with [Create React App](https://github.com/facebook/create-react-app).

## Available Scripts

In the project directory, you can run:

### `npm start`

Runs the app in the development mode.\
Open [http://localhost:3000](http://localhost:3000) to view it in your browser.

The page will reload when you make changes.\
You may also see any lint errors in the console.

### `npm test`

Launches the test runner in the interactive watch mode.\
See the section about [running tests](https://facebook.github.io/create-react-app/docs/running-tests) for more information.

### `npm run build`

Builds the app for production to the `build` folder.\
It correctly bundles React in production mode and optimizes the build for the best performance.

The build is minified and the filenames include the hashes.\
Your app is ready to be deployed!

See the section about [deployment](https://facebook.github.io/create-react-app/docs/deployment) for more information.

### `npm run eject`

**Note: this is a one-way operation. Once you `eject`, you can't go back!**

If you aren't satisfied with the build tool and configuration choices, you can `eject` at any time. This command will remove the single build dependency from your project.

Instead, it will copy all the configuration files and the transitive dependencies (webpack, Babel, ESLint, etc) right into your project so you have full control over them. All of the commands except `eject` will still work, but they will point to the copied scripts so you can tweak them. At this point you're on your own.

You don't have to ever use `eject`. The curated feature set is suitable for small and middle deployments, and you shouldn't feel obligated to use this feature. However we understand that this tool wouldn't be useful if you couldn't customize it when you are ready for it.

## Learn More

You can learn more in the [Create React App documentation](https://facebook.github.io/create-react-app/docs/getting-started).

To learn React, check out the [React documentation](https://reactjs.org/).

### Code Splitting

This section has moved here: [https://facebook.github.io/create-react-app/docs/code-splitting](https://facebook.github.io/create-react-app/docs/code-splitting)

### Analyzing the Bundle Size

This section has moved here: [https://facebook.github.io/create-react-app/docs/analyzing-the-bundle-size](https://facebook.github.io/create-react-app/docs/analyzing-the-bundle-size)

### Making a Progressive Web App

This section has moved here: [https://facebook.github.io/create-react-app/docs/making-a-progressive-web-app](https://facebook.github.io/create-react-app/docs/making-a-progressive-web-app)

### Advanced Configuration

This section has moved here: [https://facebook.github.io/create-react-app/docs/advanced-configuration](https://facebook.github.io/create-react-app/docs/advanced-configuration)

### Deployment

This section has moved here: [https://facebook.github.io/create-react-app/docs/deployment](https://facebook.github.io/create-react-app/docs/deployment)

### `npm run build` fails to minify

This section has moved here: [https://facebook.github.io/create-react-app/docs/troubleshooting#npm-run-build-fails-to-minify](https://facebook.github.io/create-react-app/docs/troubleshooting#npm-run-build-fails-to-minify)


# DOCUMENT FOR POC - React app Deployment through jenkins.


## Create 2 EC2 severs.
## One is for Jenkins and another is for the website.
## Deploy React-based app in EC2 server.
## Use Nginx as a web server to launch the website.
## Create a Github repo and push that react code in that repo.
## Create a Jenkins pipeline to deploy the code on the server.

## Step 1

Create 2 EC2 severs.(in my case )

![image](https://user-images.githubusercontent.com/88605079/175962761-f5f82205-44bb-4fb4-ac73-0f882f1c6906.png)

red-hat ==for nginx
amzon-linux ===for jenkins

•	One is for Jenkins and another is for the website.
•	Deploy React-based app in EC2 server.
•	Use Nginx as a web server to launch the website.
•	Create a Github repo and push that react code in that repo.
•	Create a Jenkins pipeline to deploy the code on the server.

Create a Jenkins pipeline to deploy the code on the server.

```
Step 2:
```

Connect to the Jenkins instance and install Jenkins there

sudo yum update –y 
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo 
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key 
sudo yum upgrade 
sudo amazon-linux-extras install java-openjdk11 -y 
sudo yum install jenkins -y 
sudo systemctl enable jenkins 
sudo systemctl start jenkins 
sudo systemctl status jenkins 

after running all the command 
Now connect to Jenkins webUI with public IP forwarded by :8080 port. 
(http://<your_server_public_DNS>:8080)
you can get more help through this sites
https://www.jenkins.io/doc/tutorials/tutorial-for-installing-jenkins-on-AWS/

Install required plugins like git, pipeline etc. 

```
Step 3:
```

In Manage Jenkins > Manage nodes and clouds > New Node.
Fill some fields like - 
Name : “<name>”
Number of executors : “<any number> (default 1 )” 
Remote root directory: “path that exists on web server instance (Jenkins Agent )” 
Label: “<any label>”
Host: “provide IP of Web Server instance” 
Credentials: “provide credentials if don’t have then add and provide username and 
ssh-key to login” 
Host key verification strategy : non-verifying verification strategy 
Save

![image](https://user-images.githubusercontent.com/88605079/175963358-b6c6b465-674e-42b3-982e-926145487a32.png)
![image](https://user-images.githubusercontent.com/88605079/175963384-11fa5831-4c8c-45f7-9f92-39e2bdc0711c.png)

then apply and save .

```
Step 4:
```

Configure new job with Pipeline type. 
In the Build Triggers section check GitHub hook trigger for GITScm polling and in Pipeline 
Definition choose Pipeline Script.

![image](https://user-images.githubusercontent.com/88605079/175963495-2ba45d62-8a18-4a96-9809-4b61d7cd381b.png)

apply and save.

```
Step 5:
```

Setup Github Webhook. 
Go to your github repository ( in my case 
https://github.com/vivekvishal011/react-app.git). 
Settings > Webhook > Add webhook.

![image](https://user-images.githubusercontent.com/88605079/175963633-ed98433a-848a-4061-9d38-5671e1abf875.png)

Replace IP to your Jenkins Instance IP in payload URL. 
Save your webhook. 

Now build your Jenkins job manually for the first time and from the next time whenever 
anything will be changed in your repository then it’s automatically going to build jenkins 
job. 
This build will start your React App on port 3000 by default, (in my case it also work on 3000)

```
Step 6: 
```

Go and connect to your web server instance with SSH command or Putty. 
All the things in this step can be also done with your Jenkins, for that either put all the 
upcoming commands at the end of steps in Jenkins pipeline script or you can create a 
shell script and push it to github repo and add an additional line in Pipeline script to run 
it.
Now Install Nginx in a web server instance. 
https://linuxconfig.org/install-nginx-on-redhat-8 
Above link can help you to install nginx step by step. 

Go to /etc/nginx/conf.d path and create a file with any name and .conf extension. 
In this file you can add two server blocks ( one for http and another for https ). 


server { 
listen 80; 
listen [::]:80; 
server_name getpocket.com; 
location / { 
return 301 https://getpocket.com/en/$request_uri; (in my case this)
} 
}

```
For https, server block : 
● Change Server name according to you
```


```
## For https, server block : 

server { 

listen 443 ssl http2 default_server;

listen [::]:443 ssl http2 default_server; 

server_name getpocket.com;
```

```
# root /usr/share/nginx/html; 

ssl_certificate /etc/letsencrypt/live/www.getpocket.com/fullchain.pem; # managed by Certbot 

ssl_certificate_key /etc/letsencrypt/live/www.getpocket.com/privkey.pem; # managed by Certbot 

ssl_session_cache shared:SSL:1m; 

ssl_session_timeout 10m; 

ssl_ciphers PROFILE=SYSTEM; 

ssl_prefer_server_ciphers on; 

location / { 

proxy_pass "http://127.0.0.1:3000/"; 

} 

error_page 404 /404.html;

location = /40x.html { 

} 

error_page 500 502 503 504 /50x.html; 

location = /50x.html { 

} 

}

```
Now, Run

```
sudo nginx -t 
sudo systemctl reload nginx
```

Both of these commands should run successfully. 
Now you can hit your web server IP to see the output. 
Thanks 
------------


[{{

while doing this you may face certain problem like 403,502 bad gatway 


let me explain this 
* for 403 (the output of this error means some permission issue 
( no route to host will say in  log )



to solve this issue we need to check the document path 
like go to /etc/nginx >nginx.conf or the server block you created 

*for 502 bad gatway
(this means that the proxy pass is not well set)
to solve this we have to configure the proxy-pass
  for this go to or open /etc/nginx/nginx.conf through any editor like vim,nano etc and place the proxy_pass under the location block of 
like the or teh same 
proxy_pass "http://127.0.0.1:3000";
}}]


### THANK YOU




