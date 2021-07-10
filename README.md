# https-spring-boot with Let’s Encrypt


1.Install Certbot

```
sudo add-apt-repository ppa:certbot/certbot
sudo apt-get update
sudo apt-get install certbot
```

2.set environment variable
* wild card is for subdomain for example api.example.com

```
DOMAIN=example.com
WILDCARD=*.$DOMAIN
echo $DOMAIN && echo $WILDCARD
example.com
*.example.com
```

3. set up manual authorization

```
sudo certbot -d $DOMAIN -d $WILDCARD --manual --preferred-challenges dns certonly
```

4. adding txt files in dns of your cloud domain
* This would be shown in your terminal copy the name _acme-challenge.example.com and Vhf1KPxSScigKnaFWOLFclDJZzeFffmzXXXXXXXXXX

```
Please deploy a DNS TXT record under the name
_acme-challenge.example.com with the following value:

Vhf1KPxSScigKnaFWOLFclDJZzeFffmzXXXXXXXXXX
```
* add txt record with name _acme-challenge.example.com and value Vhf1KPxSScigKnaFWOLFclDJZzeFffmzXXXXXXXXXX

5.verify txt is recorded

* check whether it can be retrieved with
```
dig txt _acme-challenge.example.com
```
* when you get answer section with value you entered then press enter

* chain would be saved in this folder
 ```
/etc/letsencrypt/live/example.com
 ```

6. convert to pkcs 12 format
```
 openssl pkcs12 -export -in /etc/letsencrypt/live/example.com/fullchain.pem 
                 -inkey /etc/letsencrypt/live/example.com/privkey.pem
                 -out keystore.p12 
                 -name tomcat
                 -CAfile /etc/letsencrypt/live/example.com/chain.pem
                 -caname root
```
7. add to spring boot application.properties

```
server.port: 80
security.require-ssl=true
server.ssl.key-store:/home/username/keystore.p12
server.ssl.key-store-password: <your-password>
server.ssl.keyStoreType: PKCS12
server.ssl.keyAlias: tomcat
```
* restart the server

8. check with a curl command
```
 curl https://example.com
```
* if this does not return result rather error like
```
curl: (7) Failed to connect to api.stomore.com port 443: Connection refused
```
* then change application.properties

```
server.port: 443
```

Source:
https://www.anote.dev/certbot-for-https-with-lets-encrypt/
