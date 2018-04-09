# Configure-Payara

# Physically installing the Payara/Glassfish server

1. Download the server
2. Unzip the server
3. Move the unzipped server to: /opt/
4. Add user "payara": sudo adduser payara
5. Change owner(and group) of payara folder: sudo chown -R payara:payara payara5-181 (Assuming payara5-181 is the folder located in: /opt/)
7. Be sure to cd into /opt/
6. Create a symlink to the installation folder: sudo ln -s /opt/payara5-181 payara5

Creating the symlink is a very nice way of configureing the server setup. Now your live server is on the /opt/payara5 folder.

When you upgrade to payara 182, you simply unzip it, change owner as described (user is alreadey created). And then make the symlink point at the new installation. Now your systemd configuration works with your new server, without having to edit the configuration.

Should there be broblems, and you need to revert to the previous version. Just remake the symlink to the server last known good!


# Systemd, it's really nice to use!!
Setup the server as a systemd service.

Fire off these commands on ubuntu server 17.10 -->
1. sudo touch /etc/systemd/system/payara.service
2. sudo nano /etc/systemd/system/payara.service
3. Paste in the text below, and save (Be sure to observe, and edit paths).

###Copy below this line<br/>
[Unit]<br/>
Description = Payara Server v5<br/>
After = syslog.target network.target

[Service]<br/>
User=payara<br/>
ExecStart = /usr/bin/java -jar /opt/payara5/glassfish/lib/client/appserver-cli.jar start-domain production<br/>
ExecStop = /usr/bin/java -jar /opt/payara5/glassfish/lib/client/appserver-cli.jar stop-domain production<br/>
ExecReload = /usr/bin/java -jar /opt/payara5/glassfish/lib/client/appserver-cli.jar restart-domain production<br/>
Type = forking<br/>

[Install]<br/>
WantedBy = multi-user.target<br/>
<br/>###Copy above this line<br/>

4. execute command: sudo systemctl enable payara (Enables payara server, and starts it on reboots etc.)


# You might need to add a certificate to your keystore
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
