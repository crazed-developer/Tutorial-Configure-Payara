# Configure-Payara

# Physically installing the Payara/Glassfish server

1. Download the server (example version 5.181)
2. Unzip the server
3. Rename unzipped folder to: payara5-181 (now its versioned)
4. Move the renamed folder to: /opt/
5. Add user "payara": sudo adduser payara
6. Change owner and group of payara folder: ```sudo chown -R payara:payara payara5-181 ```
7. Be sure to cd into /opt/
8. Create a symlink to the installation folder: sudo ln -s /opt/payara5-181 payara5

Creating the symlink is a very nice way of configureing the server setup. Now your live server is on the /opt/payara5 folder.

Following these instructions should result in two folders looking like this:  
```lrwxrwxrwx  1 root   root     16 Jun 12 22:14 payara5 -> /opt/payara5-181  
drwxr-xr-x  8 payara payara 4096 Mar 13 10:42 payara5-181
```

# Upgrading to the next version (example version 5.182)

1. Download the server (example version 5.182)
2. Unzip the server
3. Rename unzipped folder to: payara5-182 (Remember we versioned the initial version)
4. Move the renamed folder to: /opt/
5: Change owner(and group) of payara folder: sudo chown -R payara:payara payara5-182

Now the server is installed, but a total virgin. We need to move in the configuration and deployments from the old version of the server. So now while upgrading it's a good idea to move things a little around, to better facilitate future upgrades too.

1. From the initial 5.181 installation, copy the folder: /Users/otto/application-servers/payara5/glassfish/domains/production to /opt/production-domain
2. Change owner of the copied folder: sudo chown -R payara:payara production-domain
3. Go to the folder /Users/otto/application-servers/payara5/glassfish/domains/ in the new 5.182 installation. 
4. Move the folder production to production-bak (can also be deleted)
5. While in the folder in step 3, make a symlink to the folder in step 1 in this section: ln -s /opt/production-domain production

Your folder structure should now look like this:  
```lrwxrwxrwx  1 root   root     16 Jun 12 22:14 payara5 -> /opt/payara5-182  
drwxr-xr-x  8 payara payara 4096 Mar 13 10:42 payara5-181  
drwxr-xr-x  9 payara payara 4096 Jun  8 10:20 payara5-182  
drwxr-xr-x 12 payara payara 4096 Jun 12 22:09 production-domain  
```

Now the new server is configured to use the production domain from the initial 5.181 installation. And when the new server is will contain all previous deployments as on on initial 5.181 server.

When we made the initial 5.181 installation, we also made a symlink payara5 pointing to payara5-181. We should change this to point to the new payara5-182 installation. This will make the new installation the default, as all configruarion and ctartup scripts should point to this symlink, instead of the physical folders.

```
sudo ln -s /opt/payara5-182 payara5
```

# Systemd, it's really nice to use!!
Setup the server as a systemd service.

1. Copy the file: payara.service in this repo to this location: /etc/systemd/system
2. Execute command: sudo systemctl enable payara (Enables payara server, and starts it on reboots etc.)


# You might need to add a certificate to your Payara keystore
In order to access web resources via https. This is needed if the included keystore does not include a root certificate to support the certificate of the website/webservice you wish to access.

Adding the new certificate to your JRE does not help, as Payara/Glassfish uses it's own keystore.

This keystore is located here: /payara5/glassfish/domains/production/config/cacerts.jks (assuming you are using the production domain)

You might wish to do the same on any other domains you use, and on development machines.

In order to add a certificate simply
1. Download the certificate.
2. Execute this command: sudo keytool -importcert -file example.com.cer -keystore [path and filename to keystore file mentioned above] -alias "Friendly-name-of-your-choosing"
3. you will be prompted for a keystore password, use the same as for the JRE which is "changeit"
4. When asked type yes + enter
5. done
6. restart payara / glassfish

# You might also need to add certificates to the JRE
For the same reasons as above, it might be nesassary to add certificates to the JRE too. If you need to run processes on the server outside the Payara server.

For the Oracle JDK 8 on Ubuntu, issue the following command:

sudo keytool -importcert -file /home/user/example.com.cer -keystore /usr/lib/jvm/java-8-oracle/jre/lib/security/cacerts -alias "Friendly name of your choosing"

Depending on which JRE you have installed, some detective work on the location of the file may be needed.
