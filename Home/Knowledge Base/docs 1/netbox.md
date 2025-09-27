dnf -y update 
dnf install -y gcc libxml2-devel libxslt-devel libffi-devel libpq-devel openssl-devel redhat-rpm-config git tar
 
sed -i 's/^SELINUX=.*/SELINUX=disabled/g' /etc/selinux/config
cat /etc/selinux/config | grep SELINUX=

dnf makecache --refresh
dnf -y install python3-pip
dnf makecache --refresh
 
dnf -y install python3.9 
dnf -y install python3-requests
dnf -y install python38-Cython
dnf -y install python39-pip
 
rm -rf /usr/bin/python3
rm -rf /usr/bin/pip3
 
ln -fs /usr/bin/python3.9 /usr/bin/python3
ln -fs /usr/bin/pip3.9 /usr/bin/pip3
 
python3 --version
pip3 -V
 
dnf install -y postgresql-server
postgresql-setup --initdb
vi /var/lib/pgsql/data/pg_hba.conf
 
Replace ident for md5
 
host    all             all             127.0.0.1/32            ident
host    all             all             ::1/128                 ident
 
To: md5
host    all             all             127.0.0.1/32            md5
host    all             all             ::1/128                 md5
 
systemctl enable --now postgresql.service
 
sudo -u postgres psql
 
In the psql shell: 
               CREATE DATABASE netbox;
               CREATE USER netbox WITH PASSWORD 'my_netbox_123';
               GRANT ALL PRIVILEGES ON DATABASE netbox TO netbox;
        \q
 
Lets test:
sudo -u postgres psql --username netbox --password --host 127.0.0.1 netbox
 
dnf install -y redis
systemctl enable --now redis.service
systemctl restart --now redis.service
redis-cli ping
 
mkdir -p /opt/netbox/ && cd /opt/netbox/
sudo git clone -b master --depth 1 https://github.com/netbox-community/netbox.git .

 
groupadd --system netbox
adduser --system -g netbox netbox
chown --recursive netbox /opt/netbox/netbox/netbox/media/
 
cd /opt/netbox/netbox/netbox/
cp configuration_example.py configuration.py

vi configuration.py
               Line 11: ALLOWED_HOSTS = ['*']
               DB User/Password
               :shell  Generate Key and back to Vi
python3 ../generate_secret_key.py

cd /opt/netbox
/opt/netbox/upgrade.sh
 
source /opt/netbox/venv/bin/activate
cd /opt/netbox/netbox
python3 manage.py createsuperuser
 
ln -s /opt/netbox/contrib/netbox-housekeeping.sh /etc/cron.daily/netbox-housekeeping
 
firewall-cmd --add-port=8000/tcp --permanent
firewall-cmd --reload

source /opt/netbox/venv/bin/activate 
cd /opt/netbox
python3 netbox/manage.py runserver 0.0.0.0:8000 --insecure
 
cp /opt/netbox/contrib/gunicorn.py /opt/netbox/gunicorn.py
vi /opt/netbox/gunicorn.py
 
cp -v /opt/netbox/contrib/*.service /etc/systemd/system/
systemctl daemon-reload
 
systemctl start netbox netbox-rq
systemctl enable netbox netbox-rq
 
systemctl status netbox.service
 
ss -tunelp | grep 8001
 
dnf -y install nginx
vi /etc/nginx/conf.d/netbox.conf
 
server {
    listen 80;
    server_name 10.0.2.15;
    client_max_body_size 25m;
 
    location /static/ {
        alias /opt/netbox/netbox/static/;
    }
 
    location / {
        proxy_pass http://127.0.0.1:8001;
        proxy_set_header X-Forwarded-Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}

nginx -t
systemctl restart nginx
systemctl enable nginx
 
dnf -y install  policycoreutils-python-utils
 
semanage port -a -t dns_port_t -p tcp 8001
setsebool -P httpd_can_network_connect 1
 
firewall-cmd --permanent --add-port={80,443}/tcp
firewall-cmd --reload