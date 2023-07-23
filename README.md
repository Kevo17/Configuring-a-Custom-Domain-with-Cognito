<h1>Configuring a Custom Domain with Cognito</h1>


<h2>Description</h2>
In this hands-on lab, you will configure a number of AWS services, such as Cognito, AWS Certificate Manager (ACM), S3, and Route 53, in order to configure a custom domain for use with Cognito's hosted UI. Let's take a look at the diagram and our sample application to understand our scenario and goals for the lab a little better.
<br />

<h2>Environments Used </h2>

- <b>Windows 11</b>
- <b>AWS</b>
  
<h2>Program walk-through:</h2>

Under Pool name, enter "CognitoLab". Make sure to select the “Don’t Generate a client secret” option. Create the User Pool with other settings set to default <br/>
 
<p align="center">
<img src="https://i.imgur.com/zvvF6rZ.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

- Navigate to Request certificate. Copy Fully qualified domain name from Route 53
- Select Request a public certificate, and click Next
- In the Fully qualified domain name box type *. (that's asterisk, then dot) and then paste in the domain name you just copied from Route 53. Make sure to delete any trailing periods.
- Click Request. <br/>
 
<p align="center">
<img src="https://i.imgur.com/zvvF6rZ.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

Route 53 hosted zone name to copy to fill in for the fully qualified domain name when requesting certificate <br/>
 
<p align="center">
<img src="https://i.imgur.com/zvvF6rZ.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

Click the domain name and then click create record<br/>
 
<p align="center">
<img src="https://i.imgur.com/zvvF6rZ.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />
Create a record in Route 53:<br />
- Record name: leave this blank<br />
- Record type: A - IPv4 address<br />
- Alias (the toggle in the top-right corner): toggle On<br />
- Route traffic to:<br />
- Choose endpoint: Alias to S3 website endpoint<br />
- Choose Region: US East (N. Virginia) [us-east-1]<br />
- Enter S3 endpoint: select the listed endpoint<br />
- Create Record<br />
<br/>
 
<p align="center">
<img src="https://i.imgur.com/zvvF6rZ.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

Navigate to Create custom domain. For custom domain, use `auth.<labdomain>`. Under AWS managed certificate, select the listed ACM certificate. Click Create custom domain
 <br/>
 
<p align="center">
<img src="https://i.imgur.com/zvvF6rZ.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

Navigate to the Hosted Zones in Route 53 and select the listed domain name to create a new record. Set the following values: 
- Record name: type "auth"
- Record type: A - IPv4 address
- Alias (the toggle in the top-right corner): toggle On
- Route traffic to:
- Choose endpoint: Alias to CloudFront distribution
- Choose distribution: Paste in the Alias target you copied in the last objective
- Click Create records

 <br/>
 
<p align="center">
<img src="https://i.imgur.com/zvvF6rZ.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

- Navigate to CloudFront and Click Create a CloudFront Distribution.
- Under Origin domain, choose the domain starting with `www.<labdomain>`. followed by s3.amazonaws.com. (the bucket).
- Under Default cache behavior>Viewer protocol policy, select Redirect HTTP to HTTPS.
- Under Settings>Alternate domain name (CNAME) - optional, click Add item.
- In the Alternate domain names box, enter: `www.<labdomain>`
- Under Custom SSL certificate - optional, click on the dropdown menu and choose the ACM certificate you created.
- Under Default root object, enter "index.html".
- Click Create distribution and wait for the Status of In Progress to go to Deployed (this may take a few minutes).
<br/>
 
<p align="center">
<img src="https://i.imgur.com/zvvF6rZ.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

- Navigate back to the Route 53 Console tab.
- Click Create record and set the following values:
- Record name: enter "www"
- Record type: A - IPv4 address
- Alias (the toggle in the top-right corner): toggle On
- Route traffic to:
- Choose endpoint: Alias to CloudFront distribution
- Choose distribution: Paste in the Alias target (the distribution domain name) you copied in the last step
- Click Create records.
 <br/>
 
<p align="center">
<img src="https://i.imgur.com/zvvF6rZ.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

Ssh into the EC2 instance.<br/>
 
<p align="center">
<img src="https://i.imgur.com/zvvF6rZ.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

Clone the GitHub repo:
git clone https://github.com/linuxacademy/content-aws-sam
<br/>
 
<p align="center">
<img src="https://i.imgur.com/zvvF6rZ.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

Move into the directory:
cd content-aws-sam/labs/Configuring-Custom-Domain-Cognito/app
<br/>
 
<p align="center">
<img src="https://i.imgur.com/zvvF6rZ.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

Install dependencies:
npm install
<br/>
 
<p align="center">
<img src="https://i.imgur.com/zvvF6rZ.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

View files:
ls
<br/>
 
<p align="center">
<img src="https://i.imgur.com/zvvF6rZ.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

Edit the main.js configuration file:
vim main.js
In the Amplify.configure section, fill in the following values:
- Set the userPoolId to the user pool ID you saved in the text file.
- Set the userPoolWebClientId to the app client ID you saved in the text file.
- Set the domain to auth.<labdomain>, where <labdomain> is the one you've been using throughout this lab.
- Set redirectSignIn and redirectSignOut to `https://www.<labdomain>`
- Save and quit by pressing Escape followed by :wq.
<br/>
 
<p align="center">
<img src="https://i.imgur.com/zvvF6rZ.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

Move back into the app directory:<br/>
cd ..<br/>
Compile the application (this may take a few minutes):<br/>
npm run build<br/>

<br/>
 
<p align="center">
<img src="https://i.imgur.com/zvvF6rZ.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

Move into the dist directory:<br/>
cd dist
<br/>

<br/>
 
<p align="center">
<img src="https://i.imgur.com/zvvF6rZ.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />
Deploy the application:<br/>
aws s3 sync . `s3://www.labdomain` <br/>

<br/>

<br/>
 
<p align="center">
<img src="https://i.imgur.com/zvvF6rZ.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />

In a web browser, navigate to `https://www.<labdomain>`<br/>
Click Sign in with Cognito, and you should land at a Cognito login page.<br/>
<br/>

<br/>
 
<p align="center">
<img src="https://i.imgur.com/zvvF6rZ.png" height="80%" width="80%" alt="Disk Sanitization Step"/>
</p>

<br />
<br />
