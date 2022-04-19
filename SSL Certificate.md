cd /usr/local/src/
sudo git clone https://github.com/letsencrypt/letsencrypt
cd letsencrypt

sudo -h ./letsencrypt-auto

pip install pyopenssl ndg-httpsclient pyasn1

sudo service nginx stop
sudo -H ./letsencrypt-auto certonly --email omfamilyfoundation@gmail.com -d oriemaharaj.org
sudo service nginx start

/etc/letsencrypt/live/oriemaharaj.org


sudo vi /etc/nginx/conf.d/oriemaharaj.org.conf


ssl_certificate /etc/letsencrypt/live/oriemaharaj.org/fullchain.pem;
ssl_certificate_key /etc/letsencrypt/live/oriemaharaj.org/privkey.pem;

