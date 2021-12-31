+++
categories = ["laravel"]
date = "2021-12-19"
description = ""
featured = ""
featuredalt = ""
featuredpath = ""
linktitle = ""
slug = "setup-laravel-in-ubuntu"
title = "Setup Laravel hosting in Ubuntu using Nginx"
type = ["posts","post"]
[ author ]
  name = "Captain Snowball"
+++

## Install PHP in Ubuntu
```bash
sudo apt install php php-mbstring php-curl php-xml php-intl php-gd php-zip php-mysql zip
sudo default-mysql-server
```

### Installing Composer
```bash
curl -sS https://getcomposer.org/installer | php
sudo mv composer.phar /usr/local/bin/composer
```

### Projects Setup
Create a project in your home directory
```bash
cd ~
mkdir ~/Projects
```
And clone your project in `Projects` folder

### Setting Permissions on Projects
https://stackoverflow.com/questions/30639174/how-to-set-up-file-permissions-for-laravel
I prefer me as owner because it allows rsync to copy files to the server without any permission issues.
```bash
PROJECT_PATH=~/Projects/<project_path>

# Update folder ownership
cd $PROJECT_PATH
sudo chown -R $USER:www-data .
sudo find . -type f -exec chmod 664 {} \;
sudo find . -type d -exec chmod 775 {} \;
sudo chgrp -R www-data storage bootstrap/cache
sudo chmod -R ug+rwx storage bootstrap/cache
```

### Deployment
Put the following in the nginx virtual host file, Official docs are at: https://laravel.com/docs/8.x/deployment
```text
server {
    listen 80;
    listen [::]:80;
    server_name example.com; // Real server name
    root $PROJECT_PATH/public; // Value of $PROJECT_PATH

    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-Content-Type-Options "nosniff";

    index index.php;

    charset utf-8;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }

    error_page 404 /index.php;

    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.(?!well-known).* {
        deny all;
    }
}
```

### GitHub Actions Pipeline
If you are using GitHub Actions and a Ubuntu VPS then you can use the following config to setup auto deployment.
Create a file named `.github/workflows/deploy.yml`.

You need to set the following secrets in your GitHub repository and setup SSH and rsync in your server
- SSH_KEY: Private SSH Key
- HOST: IP address or hostname of the server
- PORT: SSH port, 22 unless you have changed it
- USER: Username of the VPS, can be ubuntu, ec-user or something else
```yaml
name: Deploy Production
on:
  push:
    branches: [master]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Install rsync
        run: sudo apt install rsync -y
      - name: Copy build
        run: |
          rm -rf .git
          rm -rf .github
          eval $(ssh-agent -s)
          echo "$SSH_KEY" | tr -d '\r' | ssh-add -
          mkdir -p ~/.ssh
          chmod 700 ~/.ssh
          ssh-keyscan -p $PORT $HOST >> ~/.ssh/known_hosts
          chmod 644 ~/.ssh/known_hosts
          rsync -az -e "ssh -p $PORT" ./ $USER@$HOST:$PROJECT_FOLDER
          ssh $USER@$HOST -p $PORT "sudo chown -R $USER:www-data $PROJECT_FOLDER"
          ssh $USER@$HOST -p $PORT "sudo find $PROJECT_FOLDER -type f -exec chmod 664 {} \;"
          ssh $USER@$HOST -p $PORT "sudo find $PROJECT_FOLDER -type d -exec chmod 775 {} \;"
          ssh $USER@$HOST -p $PORT "sudo chgrp -R www-data $PROJECT_FOLDER/storage $PROJECT_FOLDER/bootstrap/cache"
          ssh $USER@$HOST -p $PORT "sudo chmod -R ug+rwx $PROJECT_FOLDER/storage $PROJECT_FOLDER/bootstrap/cache"
          ssh $USER@$HOST -p $PORT "cd $PROJECT_FOLDER; composer install --no-dev -o"
          ssh $USER@$HOST -p $PORT "cd $PROJECT_FOLDER; php artisan migrate --force"
          ssh $USER@$HOST -p $PORT "cd $PROJECT_FOLDER; php artisan view:clear"
          ssh $USER@$HOST -p $PORT "cd $PROJECT_FOLDER; php artisan view:cache"
          ssh $USER@$HOST -p $PORT "cd $PROJECT_FOLDER; php artisan config:clear"
          ssh $USER@$HOST -p $PORT "cd $PROJECT_FOLDER; php artisan config:cache"
          ssh $USER@$HOST -p $PORT "cd $PROJECT_FOLDER; php artisan route:clear"
          ssh $USER@$HOST -p $PORT "cd $PROJECT_FOLDER; php artisan route:cache"
          ssh $USER@$HOST -p $PORT "cd $PROJECT_FOLDER; php artisan event:clear"
          ssh $USER@$HOST -p $PORT "cd $PROJECT_FOLDER; php artisan event:cache"
          ssh $USER@$HOST -p $PORT "cd $PROJECT_FOLDER; php artisan optimize"
          ssh $USER@$HOST -p $PORT "cd $PROJECT_FOLDER; php artisan storage:link"
        env:
          PROJECT_FOLDER: $PROJECT_PATH // Put real value of project path here
          SSH_KEY: ${{ secrets.SSH_KEY }}
          HOST: ${{ secrets.HOST }}
          PORT: ${{ secrets.PORT }}
          USER: ${{ secrets.USER }}
```