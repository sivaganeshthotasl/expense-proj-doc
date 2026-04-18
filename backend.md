**Backend**

**🔹The backend service handles incoming requests and interacts with the database to store and retrieve data.**

**🔹This backend service is developed using Node.js, so we need to install Node.js on the server to run the application.**

**🔹The developer has chosen Node.js for the backend service. Confirm the required version with the developer. In this case, the application supports Node.js version greater than 20.**

**🔹Install NodeJS, By default NodeJS 17 is available, We would like to enable 20 version and install this.**

**Step: Install Node.js 20**

**👉First, check the available Node.js modules:**

```
dnf module list nodejs
```

**👉Disable the default Node.js module to avoid version conflicts:**

```
dnf module disable nodejs -y
```

**👉Enable the required Node.js version (20):**

```
dnf module enable nodejs:20 -y
```

**👉Install Node.js:**

```
dnf install nodejs -y
```

**🔹Configure the Application**

**👉Create Application User to run the application:**

```
useradd --system --home /app --shell /sbin/nologin --comment "expense user" expense
```

**Explanation:**

🔹The expense user is a system (service) account used to run the application

🔹It is not intended for login, as the shell is set to /sbin/nologin

🔹Using a separate user improves security and isolation of the application

🔹The username expense is chosen to match the project name for better clarity

**Create Application Directory**

**👉Create a standard directory to store the application files:**

```
mkdir /app
```

**Explanation:**

🔹/app is used as a central location to keep application code

🔹This follows a common organizational practice for maintaining consistency

🔹It makes application management and maintenance easier

**👉Download the application code into the created /app directory.**

```
curl -o /tmp/backend.zip https://expense-builds.s3.us-east-1.amazonaws.com/expense-backend-v2.zip
```

```
cd /app
```

```
unzip /tmp/backend.zip
```

**Every application developed by a development team uses some common software components called libraries. This application also defines its required dependencies in its configuration.
**

**👉Let’s download the required dependencies.**

```
cd /app
```

```
npm install
```

**Create a systemd service for the backend application so we can start, stop, and manage it using systemctl.**

**👉Set up a systemd service for the Expense backend application.**

```
vim /etc/systemd/system/backend.service
```

```
[Unit]
Description = Backend Service

[Service]
User=expense
Environment=DB_HOST="<MYSQL-SERVER-IPADDRESS>"
ExecStart=/bin/node /app/index.js
SyslogIdentifier=backend

[Install]
WantedBy=multi-user.target
```

👉Update the database host entry:

```
Environment=DB_HOST=mysql.robossl.shop
```

**👉Reload systemd**

```
systemctl daemon-reload
```

**👉Start and Enable Service**

```
systemctl enable backend
```

```
systemctl start backend
```

**👉Verify Backend Service Status**

```
systemctl status backend
```

**You may notice that the service is in a failed state, and the error message may indicate an issue related to MySQL.**


**Why is the Backend Service Failing?**

The backend service depends on the database to function properly.

**👉At this stage:**

🔹The database server is running

🔹But no schema (tables/data structure) is loaded

🔹So, when the backend tries to connect and query the database, it fails

In simple terms:

  🔹The application is trying to use the database, but the required tables are not yet created.


**What is Missing?**

**👉For the application to work correctly, we need to load the database schema.**

🔹Schema = structure of the database (tables, columns, etc.)

🔹Without schema, the application cannot store or retrieve data

**👉Install MySQL Client**

🔹To load the schema, we need the MySQL client installed on the server.

```
dnf install mysql -y
```

**👉Load the Schema**

Once the client is installed, we can load the schema into the database:

```
mysql -h mysql.robossl.shop -uroot -pexpenseapp@1356 < /app/schema/backend.sql
```

**👉Restart Backend Service**

After loading the schema, restart the backend service:

```
systemctl restart backend
```

**👉Now check the status again:**

```
systemctl status backend
```

The service should be running successfully.

**Simple Explanation**

  1. Backend failed because database tables were missing

  2. Install MySQL client

  3. Load schema into database

  4. Restart backend → now it works




