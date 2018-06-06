# Creating Your Own Client and Server Certificates using Java Keytool

1. Create server keystore  
   * (Interactive)  
   `keytool -genkey -alias myalias -keyalg RSA -keystore keystore.jks`  

   * (Non-interactive)
      ```
      keytool -genkey -alias myalias \
     -keyalg RSA -keystore keystore.jks \
     -dname "cn=localhost, ou=IT, o=Continuent, c=US" \
     -storepass mypassword -keypass mypassword
      ```
	
2. Export Certificate  
   `keytool -export -alias myalias -file client.cer -keystore keystore.jks`

3. Import to Truststore (For client)  
   * (Interactive)  
   `keytool -import -v -trustcacerts -alias myalias -file client.cer -keystore truststore.ts`  

   * (Non-interactive)
      ```
      keytool -import -trustcacerts -alias myalias -file client.cer \
       -keystore truststore.ts -storepass mypassword -noprompt
      ```
	
Reference:
* http://docs.continuent.com/tungsten-replicator-2.1/deployment-ssl-stores.html
