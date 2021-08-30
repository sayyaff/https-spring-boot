# https-nginx with Let’s Encrypt


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

6. add this to nginx config
```
server {
  listen 443 ssl;
  server_name example.com www.example.com;

  ssl on;
  ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem
  ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem
  
 }
 ```
 * restart nginx server
