# Apache Virtual Hosts

Quick start guide to setting up an Apache virtual host environment to work with multiple applications via a single localHost server for development.

## Prerequisites

Install Apache2: `sudo apt-get update` > `sudo apt-get install apache2` 

# Create Directory Structure

It is good practice in dev mode for each virtual host directory to end with `.local`. The following pattern creates the directory folder `public_html` for `test1.local` virtual host:

```
sudo mkdir -p /var/www/test1.local/public_html
```

# Grant Permissions

Now alter the permissions to give regular users ownership so they can modify files within directories. `$USER` assumes the the value of the logged in user when the command is made:

```
sudo chown -R $USER:$USER /var/www/test1.local/public_html
```

Allow read access so content can be served correctly. The below command will trickle down to include any virtual host directories that come after the `www/`:

```
sudo chmod -R 755 /var/www
```

# Create Pages For Each Virtual Host

Open `index.html` file within the editor and create page:

```
sudo nano /var/www/test1.local/public_html/index.html
```

```html
<html>
  <head>
    <title>Welcome to test1.local!</title>
  </head>
  <body>
    <h1>Success!  The test1.local virtual host is working!</h1>
  </body>
</html>
```
Save and close: `^x , y, enter`

The file can be copied and altered for any further `index.html` files for other virtual host directories:

```
cp /var/www/test1.local/public_html/index.html /var/www/test2.local/public_html/index.html

```
The we can open these further files and modify them as required:

```
sudo nano /var/www/test2.local/public_html/index.html
```

# Create New Virtual Host Files

Apache2 comes with a default virtual host file:

```
<VirtualHost *:80>
        # The ServerName directive sets the request scheme, hostname and port that
        # the server uses to identify itself. This is used when creating
        # redirection URLs. In the context of virtual hosts, the ServerName
        # specifies what hostname must appear in the request's Host: header to
        # match this virtual host. For the default virtual host (this file) this
        # value is not decisive as it is used as a last resort host regardless.
        # However, you must set it for any further virtual host explicitly.
        #ServerName www.example.com

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html

        # Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
        # error, crit, alert, emerg.
        # It is also possible to configure the loglevel for particular
        # modules, e.g.
        #LogLevel info ssl:warn

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        # For most configuration files from conf-available/, which are
        # enabled or disabled at a global level, it is possible to
        # include a line for only one particular virtual host. For example the
        # following line enables the CGI configuration for this host only
        # after it has been globally disabled with "a2disconf".
        #Include conf-available/serve-cgi-bin.conf
</VirtualHost>

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet

```

This file can be copied and altered for each further virtual host file. **Note**: `.conf` prepends the virtual host name:

**Copy**:
```
sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/test1.local.conf
```

**Open**:
```
sudo nano /etc/apache2/sites-available/test1.local.conf
```
Once altered save and close. Lets disect the virtual host file for the example:

```js
<VirtualHost *:80>
    ServerAdmin admin@test1.local // Email for admin
    ServerName test1.local // Incoming end-point
    ServerAlias www.test1.local // further incoming end-points
    DocumentRoot /var/www/test1.local/public_html // where the content is served from
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

```

# Enable Virtual Host Files

Once the virtual hosts have been set up they need to be enabled:

```
sudo a2ensite test1.local.conf
```
Once all virtual hosts have been enabled, disable the default site:

```
sudo a2dissite 000-default.conf
```

Finally restart the Apache for all changes to take effect:

```
sudo systemctl restart apache2
```

# Set Up LocalHost File Config

To access the file via the editor:

```
sudo nano /etc/hosts
```
Then within the file each virtual host is set to localhost:

```js
127.0.0.1       localhost
127.0.1.1       Debian.xxx     Debian
127.0.0.1       test1.local
127.0.0.1       test2.local

```

# Test

At this point the following is in place:

1. The virtual directories 
2. Access permissions for user and server
3. Root documents have been created and configured
4. The virtual host files have been enabled
5. The server has been configured for local host development

Its time to test:

`test1.local`
