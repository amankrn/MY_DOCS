## Odoo Configuration?

**Odoo Configuration** refers to the process of setting up and customizing the Odoo ERP (Enterprise Resource Planning) system to meet specific business requirements. This involves several steps, including installing the software, setting up the database, and adjusting various settings through a configuration file to optimize performance, security, and functionality.

1. **Database Connection**: To connect Odoo to the correct PostgreSQL database.
2. **Performance Tuning**: To adjust settings like memory limits and worker processes for better performance.
3. **Security**: To set up passwords and access controls.
4. **Customization**: To specify paths for custom modules and add-ons.
5. **Logging**: To define where and how Odoo logs its activities for monitoring and debugging.

### How Odoo Configuration Helps in Development

1. **Module Configuration**:
   - **Enabling/Disabling Modules**: Activate only the modules needed for the business, reducing clutter and improving performance.
   - **Module Settings**: Customize module settings to fit specific business processes.

2. **User and Access Rights**:
   - **User Roles**: Define roles and permissions to control access to different parts of the system.
   - **Security**: Ensure data security by configuring access rights appropriately.

3. **Database Configuration**:
   - **Multi-Company Setup**: Configure the system to handle multiple companies within a single database.
   - **Data Backup and Restore**: Set up automated backups to prevent data loss.

4. **Localization**:
   - **Language and Currency**: Configure the system to support multiple languages and currencies.
   - **Tax Configuration**: Set up tax rules according to local regulations.

5. **Workflow Automation**:
   - **Automated Actions**: Create automated workflows to streamline repetitive tasks.
   - **Scheduled Actions**: Set up scheduled tasks to run at specific intervals.

6. **Reporting and Analytics**:
   - **Custom Reports**: Configure custom reports to provide insights into business operations.
   - **Dashboards**: Set up dashboards for real-time monitoring of key performance indicators (KPIs).

7. **Integration with Other Systems**:
   - **APIs**: Configure APIs to integrate Odoo with other applications.
   - **Third-Party Modules**: Install and configure third-party modules to extend functionality.

---
### 1. **Setting Up the Development Environment**

#### **Installing Odoo**
First, you need to install Odoo. You can follow the official installation guide for your operating system. For example, on Ubuntu, you can use the following commands:

```bash
sudo apt-get update
sudo apt-get install postgresql
sudo apt-get install python3-pip
pip3 install -r requirements.txt
```

### 2. **Creating and Configuring the Database**

#### **PostgreSQL Setup**
Odoo uses PostgreSQL as its database management system. You need to create a PostgreSQL user and a database for Odoo:

```bash
sudo -u postgres createuser -s odoo
sudo -u postgres createdb odoo
```

### 3. **Understanding the Configuration File**

The Odoo configuration file (`odoo.conf`) is crucial for setting up your development environment. Here’s a detailed explanation of the key terms and how to configure them:

#### **Creating the Configuration File**
You can create the configuration file using the following command:

```bash
odoo-bin -c /etc/odoo/odoo.conf
```

#### **Key Configuration Parameters**

1. **[options]**: This section contains all the configuration options.
2. **db_host**: The hostname of the database server. For local development, this is usually `localhost`.
3. **db_port**: The port number on which the database server is listening. The default is `5432`.
4. **db_user**: The PostgreSQL user that Odoo will use to connect to the database.
5. **db_password**: The password for the PostgreSQL user.
6. **addons_path**: The path to the Odoo add-ons directory.
7. **admin_passwd**: The master password for managing databases.

#### **Example Configuration File**

Here’s an example of what your `odoo.conf` might look like:

```ini
[options]
; This is the password that allows database operations:
admin_passwd = admin
db_host = localhost
db_port = 5432
db_user = odoo
db_password = odoo
addons_path = /usr/lib/python3/dist-packages/odoo/addons
logfile = /var/log/odoo/odoo.log
```

### 4. **Running Odoo with the Configuration File**

Once your configuration file is set up, you can start Odoo using the following command:

```bash
odoo-bin -c /etc/odoo/odoo.conf
```

### Detailed Explanation of Each Term

- **admin_passwd**: This is the master password used for database management operations like creating or dropping databases.
- **db_host**: Specifies the hostname of the PostgreSQL server. For local development, this is typically `localhost`.
- **db_port**: The port number on which PostgreSQL is running. The default PostgreSQL port is `5432`.
- **db_user**: The PostgreSQL user that Odoo will use to connect to the database.
- **db_password**: The password for the PostgreSQL user specified in `db_user`.
- **addons_path**: The directory path where Odoo add-ons (modules) are located. This can be a single path or multiple paths separated by commas.
- **logfile**: The file where Odoo logs its activities. This is useful for debugging and monitoring.

### Additional Tips

- **Security**: Ensure your `admin_passwd` is strong and not easily guessable.
- **Multiple Databases**: If you are working with multiple databases, you can use the `dbfilter` 


### Additional Configuration Parameters

1. **dbfilter**: This parameter is used to filter the databases that Odoo can access based on the hostname.
   ```ini
   dbfilter = ^%d$
   ```

2. **log_level**: Sets the logging level. Possible values are `info`, `debug`, `warn`, `error`.
   ```ini
   log_level = info
   ```

3. **workers**: Specifies the number of worker processes for handling HTTP requests. This is useful for improving performance in a production environment.
   ```ini
   workers = 4
   ```

4. **max_cron_threads**: Defines the number of threads to use for handling scheduled jobs (cron jobs).
   ```ini
   max_cron_threads = 2
   ```

5. **limit_memory_soft**: Sets the soft limit for memory usage per worker. If a worker exceeds this limit, it will be recycled after the current request.
   ```ini
   limit_memory_soft = 640000000
   ```

6. **limit_memory_hard**: Sets the hard limit for memory usage per worker. If a worker exceeds this limit, it will be killed.
   ```ini
   limit_memory_hard = 760000000
   ```

7. **limit_time_cpu**: Sets the maximum CPU time (in seconds) a request can take before being killed.
   ```ini
   limit_time_cpu = 60
   ```

8. **limit_time_real**: Sets the maximum real time (in seconds) a request can take before being killed.
   ```ini
   limit_time_real = 120
   ```

9. **proxy_mode**: Enables proxy mode, which is useful if Odoo is behind a reverse proxy.
   ```ini
   proxy_mode = True
   ```

10. **smtp_server**: Specifies the SMTP server for sending emails.
    ```ini
    smtp_server = smtp.gmail.com
    ```

11. **smtp_port**: Specifies the port for the SMTP server.
    ```ini
    smtp_port = 587
    ```

12. **smtp_user**: The username for the SMTP server.
    ```ini
    smtp_user = your_email@gmail.com
    ```

13. **smtp_password**: The password for the SMTP server.
    ```ini
    smtp_password = your_password
    ```

14. **db_maxconn**: Sets the maximum number of connections to the database.
    ```ini
    db_maxconn = 64
    ```

15. **db_name**: Specifies the name of the database to use. If not set, Odoo will use the database specified in the URL.
    ```ini
    db_name = odoo
    ```

16. **without_demo**: Disables loading demo data for the specified modules.
    ```ini
    without_demo = all
    ```

### Example Configuration File with Additional Parameters

Here’s an example of an `odoo.conf` file with some of these additional parameters:

```ini
[options]
admin_passwd = admin
db_host = localhost
db_port = 5432
db_user = odoo
db_password = odoo
addons_path = /usr/lib/python3/dist-packages/odoo/addons
logfile = /var/log/odoo/odoo.log
log_level = info
workers = 4
max_cron_threads = 2
limit_memory_soft = 640000000
limit_memory_hard = 760000000
limit_time_cpu = 60
limit_time_real = 120
proxy_mode = True
smtp_server = smtp.gmail.com
smtp_port = 587
smtp_user = your_email@gmail.com
smtp_password = your_password
db_maxconn = 64
db_name = odoo
without_demo = all
```