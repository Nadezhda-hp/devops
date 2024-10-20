1. Заходим  в терминал на маке и вводим
   sudo apt update
sudo apt install nginx

Сформируем новый или измените существующий конфигурационный файл для своего сайта. К примеру, можно создать файл /etc/nginx/sites-available/example.com.conf:
server {
    listen 80;
    server_name example.com www.example.com;

    Перенаправление всех HTTP на HTTPS
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name example.com www.example.com;

    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem; # Путь к вашему сертификату
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem; # Путь к вашему закрытому ключу
    
    
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;

    
    root /var/www/example.com/html;
    index index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }

    
    location /images/ {
        alias /var/www/example.com/images/; # Путь к папке с изображениями
    }

    location /files/ {
        alias /var/www/example.com/files/; # Путь к папке с файлами
    }

    
    error_page 404 /404.html;
    location = /404.html {
        internal;
    }
}

 Конфигурация для другого домена (если необходимо)
 server {
     listen 80;
     server_name anotherdomain.com www.anotherdomain.com;
     ...
 }
2.Потом чтобы активировать новую конфигурацию, создаем символическую ссылку на ваш файл в директории sites-enabled

проверяем:
sudo nginx -t

3. перезапускаем:
sudo systemctl restart nginx


