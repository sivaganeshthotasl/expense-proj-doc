**MySQL**

The developer has selected MySQL as the database, so we need to install and set it up.

Before installing, we should check with the developer about which version of MySQL to use. This is important because the application may only work properly with a specific version.

**👉Install MySQL Server version 8.0.x as per the application requirement**

```
dnf install mysql-server -y
```

**👉Enable and Start the MySQL service to make the database server operational**

```
systemctl enable mysqld
systemctl start mysqld
```

Note: After starting the MySQL service, change the default root user password before using the database.

**👉Set the password to expenseapp@1356 (or choose a secure password as per your requirement)**

```
mysql_secure_installation --set-root-pass expenseapp@1356
```

**Why do we change the default root password?**
By default, MySQL may have a temporary or weak password for the root user. Keeping it unchanged can lead to security risks.

1. The root user has full control over the database (create, delete, modify everything).

2. If the default password is not changed, unauthorized users may gain access.

3. Changing the password ensures the database is secure and protected from misuse.

4. It is a standard best practice followed in all production and even development environments.


**Verification of MySQL Setup**

After completing the installation and configuration, we need to verify that the MySQL database is working correctly.

**👉Create DNS Record for MySQL Server**

Before connecting using a hostname, create a DNS record in Amazon Route 53.

Create an A record for the database server

Example:

  → Name: mysql.robossl.shop
  → Value: MySQL Server Private IP

This allows us to connect using a friendly hostname instead of an IP address.

**👉Verify DNS Resolution**

Run the below command to confirm that the DNS is resolving correctly:

```
nslookup mysql.robossl.shop
```

If it returns the correct IP address, DNS setup is successful.

**👉Connect to MySQL Server**

**👉We use the MySQL client package (mysql) to connect to the database:**

```
mysql -h <host-address> -u root -p<password>
```

**👉Example using DNS:**

```
mysql -h mysql.robossl.shop -uroot -p<password>
```

-h → Hostname or IP address of MySQL server
-u → Username (root)
-p → Prompts for password

**👉If both MySQL client and server are on the same machine, you can simply run:**

```
mysql
```

This connects to the local MySQL server using default settings.


**👉Verify Databases**

**Once connected, you will get the MySQL prompt. Run:**

```
show databases;
```

This displays all available databases (schemas).

**👉Select a Database**

Choose a database to work with:

```
use <database>;
```

**👉To check tables inside the selected database:**

```
show tables;
```

**👉To view the data inside a table:**

```
select * from <table_name>;
```

**Simple Explanation**

**1. First, create a DNS record so we can use a hostname instead of an IP**

**2. Verify the DNS is working**

**3. Connect to MySQL using the client**

**4. Check databases, tables, and data to confirm everything is working properly**
