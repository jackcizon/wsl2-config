# WSL2 PHP Development Setup

## Set Password for Root

```bash
sudo su
sudo apt update
sudo apt upgrade
```

---

## Install PHP

```bash
sudo apt install php
which apache2
sudo apt update
```

---

## Install GUI Apps

```bash
sudo apt install gedit -y
sudo apt install nautilus -y
sudo apt install vlc -y
sudo apt install x11-apps -y
sudo apt install firefox
sudo apt update
cd /tmp
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
sudo apt install --fix-missing ./google-chrome-stable_current_amd64.deb
```

---

## Install and Configure Git

```bash
sudo apt-get install git
git config --global user.name "yourname"
git config --global user.email "yourname@emaildomain"
```

---

## Install MySQL and Set Password for Root

```bash
sudo apt update
mysql --version
sudo apt install mysql-server
systemctl status mysql
sudo mysql
mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'yourpassword';
mysql -u root -p
systemctl status mysql
```

---

## Install Redis

```bash
sudo apt update
sudo apt install redis-server
sudo service redis-server start
sudo service redis-server status
```

### Use `phpredis` Client in a Project

```bash
composer require predis/predis
```

### Configure Redis

Add to `.env`:

```dotenv
CACHE_DRIVER=redis
```

In `database.php`:

```php
'redis' => [
    'client' => env('REDIS_CLIENT', 'phpredis'),
    'options' => [
        'cluster' => env('REDIS_CLUSTER', 'redis'),
        // Uncomment the following line if no prefix is required
        // 'prefix' => env('REDIS_PREFIX', Str::slug(env('APP_NAME', 'laravel'), '_').'_database_'),
    ],
]
```

In `cache.php`:

```php
// Set default cache driver
'default' => 'redis',

// Optional comment
// 'prefix' => env('CACHE_PREFIX', Str::slug(env('APP_NAME', 'laravel'), '_').'_cache_'),
```

---

## Use Docker

```bash
docker image ls --all
docker run hello-world
sudo apt update
```

---

## Install C/C++ Tools

```bash
sudo apt install g++ gdb make ninja-build rsync zip
sudo apt-get install curl
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/master/install.sh | bash
```

---

## Install Node.js

```bash
command -v nvm
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"
nvm install --lts
nvm ls
which npm
```

---

## Configure `php.ini`

```bash
sudo apt install php8.1-mbstring php8.1-mysql php8.1-sockets php8.1-bz2 php8.1-gd php8.1-ftp php8.1-curl php8.1-openssl php8.1-xdebug
```

---

## Configure Apache

Edit the following files:

- `/etc/apache2/apache2.conf`
- `/etc/apache2/sites-available/000-default.conf`

Set web server user:

```bash
sudo chown -R www-data:www-data dir_path
```

Run as root:

```bash
chmod -R 777 /var/www
rm -r html
touch index.php
```

Add the following to your configuration:

```apache
DocumentRoot /var/www

<Directory /var/www>
    Options Indexes FollowSymLinks
    AllowOverride All
    Require all granted
</Directory>
```

---

## Configure Virtual Hosts

Create a file in `/etc/apache2/sites-available/your_domain.conf`:

```apache
<VirtualHost your_domain:80>
    ServerName your_domain
    DocumentRoot /var/www/your_domain/public

    <Directory /var/www/your_domain />
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/your_domain-error.log
    CustomLog ${APACHE_LOG_DIR}/your_domain-access.log combined
</VirtualHost>
```

Add a soft link:

```bash
sudo ln -s /etc/apache2/sites-available/yourdomain.com.conf /etc/apache2/sites-enabled/
```

Edit `/etc/hosts`:

```bash
127.0.0.1 your_domain
```

---

## Fix Laravel App Code 500 Error

```bash
sudo chown -R www-data:www-data /var/www/laravel-app
sudo chmod -R 777 /var/www/laravel-app
```

---

## Set Default Charset

```bash
export LANG=en_US.UTF-8
```

---

## Configure Nginx for PHP-FPM

1. Stop Apache:

```bash
sudo systemctl stop apache2
```

2. Create `/etc/nginx/sites-available/site1.com`:

```nginx
   server {
       listen 80;
       listen [::]:80;

       server_name site1.com www.site1.com;

       root /var/www/site1.com/public;
       index index.html index.htm index.php;

       location / {
           try_files $uri $uri/ /index.php?$query_string;
       }

       location ~ \.php$ {
           include snippets/fastcgi-php.conf;
           fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
       }

       location ~ /\.ht {
           deny all;
       }
   }
   ```

1. Add a soft link:

   ```bash
   sudo ln -s /etc/nginx/sites-available/site1.com /etc/nginx/sites-enabled/
   ```

2. Check PHP-FPM status:

   ```bash
   sudo systemctl status php8.1-fpm
   ```

---

## Conda Management

### install 

```bash
wget https://repo.anaconda.com/archive/Anaconda3-2023.07-1-Linux-x86_64.sh -O ~/anaconda.sh
bash ~/anaconda.sh
~/anaconda3/bin/conda init
```

### Basic Commands

```bash
conda create -n myenv python=3.8
conda remove -n myenv --all
conda create --name cloned_env --clone myenv
conda activate myenv
```

### Export and Import via YAML

Export:

```bash
conda env export > environment.yml
```

Create from YAML:

```bash
conda env create -f environment.yml
```

Sample `environment.yml`:

```yaml
name: myenv
channels:
  - conda-forge
  - defaults
dependencies:
  - python=3.8
  - numpy=1.21
  - pandas
  - pip:
      - django==3.2.5
      - requests>=2.26.0
```

---

## Requirements File for Pip

Export dependencies:

```bash
pip freeze > requirements.txt
```

Install from `requirements.txt`:

```bash
pip install -r requirements.txt
```
