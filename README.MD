### Connecting to the VPS

To connect your VPS server, you can use your server IP, you can create a root password and enter the server with your IP address and password credentials. But the more secure way is using an SSH key.

### Creating SSH Key

### For MAC OS / Linux / Windows 10 (with openssh)

1. Launch the Terminal app.
2. ```ssh-keygen -t rsa```
3. Press ```ENTER``` to store the key in the default folder /Users/anuragdeep/.ssh/id_rsa).

4. Type a passphrase (characters will not appear in the terminal).

5. Confirm your passphrase to finish SSH Keygen. You should get an output that looks something like this:

``` Your identification has been saved in /Users/anuragdeep/.ssh/id_rsa.
Your public key has been saved in /Users/anuragdeep/.ssh/id_rsa.pub.
The key fingerprint is:
a3:81:92:cb:85:d6:5a:f4:7c:1f:c2:43:fd:c6:44:30 anuragdeep@mac.local
The key's randomart image is:
+--[ RSA 2048]----+
|                 |
|         .       |
|        E .      |
|   .   . o       |
|  o . . S .      |
| + + o . +       |
|. + o = o +      |
| o...o * o       |
|.  oo.o .        |
+-----------------+ 
```
6. Copy your public SSH Key to your clipboard using the following code:
```pbcopy < ~/.ssh/id_rsa.pub```

### For Windows
1. Download PuTTY and PuTTYgen.
2. Open up PuTTYgen and click the ```Generate```.
3. Copy your key.
4. Enter a key passphrase and confirm.
5. Save the private key.


## Connection

After copying the SSH Key go the to hosting service provider dashboard and paste your key and save. After,

### For MAC OS / Linux

```bash
ssh root@<server ip address> 
```

### For Windows

1. Open the PuTTY app.
2. Enter your IP address.
3. Open the following section:
Connection - SSH - Auth
4. Browse the folders and choose your private key.

## First Configuration

### Deleting apache server

```
systemctl stop apache2
```

```
systemctl disable apache2
```

```
apt remove apache2
```

to delete related dependencies:
```
apt autoremove
```

### Cleaning and updating server
```
apt clean all && sudo apt update && sudo apt dist-upgrade
```

```
rm -rf /var/www/html
```

### Installing Nginx

```
apt install nginx
```

### Installing and configure Firewall

```
apt install ufw
```

```
ufw enable
```

```
ufw allow "Nginx Full"
```

## First Page

#### Delete the default server configuration

```
 rm /etc/nginx/sites-available/default
```

```
 rm /etc/nginx/sites-enabled/default
```

#### First configuration
```
 nano /etc/nginx/sites-available/anuragdeep.com
```

```
server {
  listen 80;

  location / {
        root /var/www/anuragdeep.com;
        index  index.html index.htm;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
        try_files $uri $uri/ /index.html;
  }
}

```

```
ln -s /etc/nginx/sites-available/anuragdeep.com /etc/nginx/sites-enabled/anuragdeep.com

```

##### Write your fist message
```
nano /var/www/anuragdeep.com/index.html

```

##### Start Nginx and check the page

```
systemctl start nginx
```

## Uploading Apps Using Git

```
apt install git
```

```
mkdir anuragdeep.com
```
```
cd anuragdeep.com
```

```
git clone <your repository>
```

## Nginx Configuration for new apps
```
nano /etc/nginx/sites-available/anuragdeep.com
```
```
location /api {
        proxy_pass http://127.0.0.0.1:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
  }
```

##### If you check the location /api you are going to get "502" error which is good. Our configuration works. The only thing we need to is running our app

```
apt install nodejs
```

```
apt install npm
```

```
cd api
```
```
npm install
```
```
nano .env
```
##### Copy and paste your env file
```
node index.js
```

#### we will use pm2 to keep running applications ```pm2```
```
npm i -g pm2
```
Let's create a new pm2 instance in backend strapi folder

```
pm2 start npm --name be -- run start

```
and fe instance in nextjs folder

```
pm2 start npm --name fe -- run start

```

```
pm2 startup ubuntu 
```

## React App Deployment

```
cd ../client
```

```
nano .env
```
Paste your env file.

```
npm i
```
Let's create the build file

```
npm run build
```

```
### Adding Domain
1 - Make sure that you created your A records on your domain provider website.

2 - Change your pathname from Router

3 - Change your env files and add the new API address 

4 - Add the following server config in nginx at location /etc/nginx/sites-available/
```

```
NextJS
server {
    listen 80;
    server_name anuragdeep.com www.anuragdeep.com;

    location ^~ /_next/static/ {
        alias /var/www/server/site/.next/static/;
        access_log on;
    }

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}

Strapi NodeJS
server {
  listen 80;
  server_name api.anuragdeep.com;
  location / {
    proxy_pass http://127.0.0.0.1:1337;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection 'upgrade';
    proxy_set_header Host $host;
    proxy_cache_bypass $http_upgrade;
    }
}

```

After editing nginx configuration run

```
sudo systemctl restart nginx
```

## SSL Certification
```
apt install certbot python3-certbot-nginx
```

Make sure that Nginx Full rule is available
```
ufw status
```

```
certbot --nginx -d example.com -d www.example.com
```

Let’s Encrypt’s certificates are only valid for ninety days. To set a timer to validate automatically:
```
systemctl status certbot.timer
```

