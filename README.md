# WildFly (JBoss) Installation and Configuration on Ubuntu

This guide outlines the steps to install and configure WildFly (JBoss) on Ubuntu, change default ports, create an administrator user, and set it up as a system service.

---

## **1. Download and Extract WildFly**

### Download WildFly:
```bash
wget https://github.com/wildfly/wildfly/releases/download/35.0.0.Final/wildfly-35.0.0.Final.tar.gz
```

### Extract the downloaded archive:
```bash
tar -xvzf wildfly-35.0.0.Final.tar.gz
```

### Move WildFly to `/opt/jboss`:
```bash
sudo mv wildfly-35.0.0.Final /opt/jboss
```

### Set ownership of the WildFly directory:
```bash
sudo chown -R $USER:$USER /opt/jboss
```

---

## **2. Change Default Ports**

### Edit the WildFly configuration file:
```bash
nano /opt/jboss/standalone/configuration/standalone.xml
```

### Change the HTTP port to `8181`:
```xml
<socket-binding name="http" port="${jboss.http.port:8181}"/>
```

### Change the HTTPS port to `8543`:
```xml
<socket-binding name="https" port="${jboss.https.port:8543}"/>
```

### Save and close the file (`CTRL + O`, then `CTRL + X`).

---

## **3. Start WildFly**

### Navigate to the WildFly `bin` directory:
```bash
cd /opt/jboss/bin
```

### Start the server in standalone mode:
```bash
./standalone.sh
```

### Verify in your browser:
```
http://your-ec2-public-ip:8181
```

---

## **4. Create an Administrator User**

### Run the script to create users:
```bash
/opt/jboss/bin/add-user.sh
```

### Follow the instructions to create a user with the `ManagementRealm` role.

---

## **5. Set WildFly as a System Service**

### Create a systemd service file:
```bash
sudo nano /etc/systemd/system/jboss.service
```

### Add the following configuration:
```ini
[Unit]
Description=WildFly Application Server
After=network.target

[Service]
User=your-username
Group=your-username
ExecStart=/opt/jboss/bin/standalone.sh -b 0.0.0.0
ExecStop=/opt/jboss/bin/jboss-cli.sh --connect command=:shutdown
Restart=always

[Install]
WantedBy=multi-user.target
```

### Save and close the file (`CTRL + O`, then `CTRL + X`).

### Reload systemd and enable the service:
```bash
sudo systemctl daemon-reload
sudo systemctl enable jboss
```

### Start the WildFly service:
```bash
sudo systemctl start jboss
```

### Check the service status:
```bash
sudo systemctl status jboss
```

---

WildFly (JBoss) is now installed, configured, and running as a system service. You can access it via the updated HTTP or HTTPS ports.
