**Frontend**

**🔸The frontend service in the Expense application is responsible for serving web content to users. It provides the user interface (UI) of the application.This frontend consists of static content such as HTML, CSS, and JavaScript.To serve static content, we need a web server.**

**🔸The developer has chosen Nginx as the web server, so we will install and configure Nginx to host the frontend application.**

**👉Install the Nginx web server, then enable and start the Nginx service.**

```
dnf install nginx -y
```

```
systemctl enable nginx
````

```
systemctl start nginx
```

**👉Delete the default Nginx web content.**

```
rm -rf /usr/share/nginx/html/*
```

**👉Download the frontend application content**

```
curl -o /tmp/frontend.zip https://expense-builds.s3.us-east-1.amazonaws.com/expense-frontend-v2.zip
```

**👉Unpack the frontend content.**

```
cd /usr/share/nginx/html
```

```
unzip /tmp/frontend.zip
```

**Access the Nginx service again through a web browser and verify that the Expense application content is displayed.**

**🔸Configure Nginx Reverse Proxy**

**Create a reverse proxy configuration in Nginx to forward client requests to the backend service.**

**Why Do We Need a Reverse Proxy? (Simple Explanation)**

👉The frontend (Nginx) serves static content like HTML, CSS, and JavaScript

👉The backend runs on a different port (for example: localhost:8080)

👉Users should not directly access the backend service

**So, Nginx acts as a middle layer:**

👉It receives requests from users

👉Forwards API requests to the backend

👉Sends the response back to the user

**Nginx works like a bridge between the user and the backend service.Users talk to Nginx, and Nginx talks to the backend.**

**Create Nginx Reverse Proxy Configuration.**

Before creating a Conf file we need to create a backend dns recond in the route 53

**👉Create DNS Record for Backend**

**Create a DNS record in Amazon Route 53:**

---> Record Type: A
---> Name: backend.robossl.shop
---> Value: (Backend Server Private IP)

**Note: Update Frontend Public Ipv4 record in the robossl.shop host. you just edit and replace with new public ipv4 ip.**

**Verify DNS:**

```
nslookup backend.robossl.shop
```

Ensure it resolves to the correct backend server IP.

**👉Create Nginx Reverse Proxy Configuration**

```
vim /etc/nginx/default.d/expense.conf
```

**Add the following content**

```
proxy_http_version 1.1;

location /api/ { proxy_pass http://localhost:8080/; }

location /health {
  stub_status on;
  access_log off;
}
```

**Note: Ensure you replace localhost with the actual IP address or DNS name of the backend server in the Nginx configuration.**

**👉Restart Nginx Service to load the changes of the configuration.**

```
systemctl restart nginx
```



**End-to-End Application Verification**

After completing the frontend, backend, and database setup, verify that the application is working correctly.

**Step 1: Access the Application**

Open the application in a web browser using the server URL or domain

Navigate to the “Add Expense” page

**Step 2: Add Expense Data**

**👉Enter the following details:**

  ---> Amount: (any value, e.g., 1000)
  
  ---> Description: travel

  ---> click on add 

  ---> you can see the response details

  
**Step 3: Verify Data in Database**

  ---> Connect to the MySQL database:

  ```
mysql -h mysql.robossl.shop -u root -p
```

**Step 4: Check Database and Tables**

```
show databases;
```

```
use <database_name>;
```

```
show tables;
```

**Step 5: Verify Inserted Data**

```
select * from <table_name>;
```

**You should see the newly added record (e.g., description: travel) in the table.**







