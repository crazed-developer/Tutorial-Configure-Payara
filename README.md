# Configure-Payara

# You might need to add a certificate to your keystore
In order to access web resources via https. This is needed if the included keystore does not include a root certificate to support the certificate of the website/webservice you wish to access.

Adding the new certificate to your JRE does not help, as Payara/Glassfish uses it's own keystore.

This keystore is located here: /payara5/glassfish/domains/production/config/cacerts.jks

In order to add a certificate simply
1. Download the certificate.
2. Execute this command: sudo keytool -importcert -file example.com.cer -keystore [path and filename to keystore file mentioned above] -alias "Friendly-name-of-your-choosing"
3. you will be prompted for a keystore password, use the same as for the JRE which is "changeit"
4. When asked type yes + enter
5. done
6. restart payara / glassfish
