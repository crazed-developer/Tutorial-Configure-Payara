# Configure-Payara

# Systemd, it's really nice to use!!
Setup the server as a systemd service.

Fire off these commands on ubuntu server 17.10 -->
1. sudo touch /etc/systemd/system/payara.service
2. sudo nano /etc/systemd/system/payara.service
3. Paste in the text below, and save (Be sure to observe, and edit paths).

// Copy below this line
[Unit]
Description = Payara Server v5
After = syslog.target network.target

[Service]
User=payara
ExecStart = /usr/bin/java -jar /opt/payara5/glassfish/lib/client/appserver-cli.jar start-domain production
ExecStop = /usr/bin/java -jar /opt/payara5/glassfish/lib/client/appserver-cli.jar stop-domain production
ExecReload = /usr/bin/java -jar /opt/payara5/glassfish/lib/client/appserver-cli.jar restart-domain production
Type = forking

[Install]
WantedBy = multi-user.target
// Copy above this line 

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
