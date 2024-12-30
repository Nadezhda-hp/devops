# Отчет по лабораторной работе

## Тема
Настройка Nginx для безопасного подключения через HTTPS и поддержка нескольких доменных имен.

## Цель работы
Настроить Nginx так, чтобы:
1. Подключение осуществлялось через HTTPS с сертификатом.
2. HTTP-запросы (порт 80) перенаправлялись на HTTPS (порт 443).
3. Использовались псевдонимы путей к файлам (alias).
4. Были настроены виртуальные хосты для обслуживания нескольких доменных имен.

## Ход работы

### 1. Установка Nginx
#### Команды:
brew install nginx

#### Проверка установки:
nginx -v
*Вывод:* Установлена последняя версия Nginx.

---

### 2. Настройка HTTPS

#### Генерация самоподписанного сертификата:
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /usr/local/etc/nginx/server.key -out /usr/local/etc/nginx/server.crt
*Заполненные данные:*
- Country Name: RU
- State: StPetersburg
- Locality: StPetersburg
- Organization: MyLab
- Common Name: localhost

#### Настройка конфигурации Nginx:
В файле /usr/local/etc/nginx/nginx.conf добавлен следующий блок для HTTPS:
server {
    listen 443 ssl;
    server_name localhost;

    ssl_certificate /usr/local/etc/nginx/server.crt;
    ssl_certificate_key /usr/local/etc/nginx/server.key;

    location / {
        root /usr/local/var/www;
        index index.html;
    }
}
---

### 3. Принудительное перенаправление HTTP на HTTPS

#### Добавлено в конфигурацию:
server {
    listen 80;
    server_name localhost;

    return 301 https://$host$request_uri;
}
*Проверка:*
curl -I http://localhost
*Вывод:* HTTP-запрос перенаправляется на HTTPS.

---

### 4. Настройка псевдонимов (alias)

#### Добавлено:
location /static/ {
    alias /usr/local/var/www/static/;
}
Создан каталог и файл:
mkdir -p /usr/local/var/www/static
echo "Alias test" > /usr/local/var/www/static/test.html
*Проверка:*
curl https://localhost/static/test.html
*Вывод:* Страница успешно отображается.

---

### 5. Настройка виртуальных хостов

#### Добавлено:
Для домена example1.local:
server {
    listen 443 ssl;
    server_name example1.local;

    ssl_certificate /usr/local/etc/nginx/server.crt;
    ssl_certificate_key /usr/local/etc/nginx/server.key;

    root /usr/local/var/www/example1;
    index index.html;
}
Для домена example2.local:
server {
    listen 443 ssl;
    server_name example2.local;

    ssl_certificate /usr/local/etc/nginx/server.crt;
    ssl_certificate_key /usr/local/etc/nginx/server.key;

    root /usr/local/var/www/example2;
    index index.html;
}

#### Созданы каталоги и файлы:
mkdir -p /usr/local/var/www/example1
mkdir -p /usr/local/var/www/example2

echo "Example 1" > /usr/local/var/www/example1/index.html
echo "Example 2" > /usr/local/var/www/example2/index.html

#### Настройка локальных доменов:
Файл /etc/hosts дополнен:
127.0.0.1 example1.local
127.0.0.1 example2.local
*Проверка:*
curl https://example1.local
curl https://example2.local
*Вывод:* Обе страницы отображаются корректно.

---

## Результаты работы
1. Nginx установлен на MacBook.
2. Подключение через HTTPS настроено с использованием самоподписанного сертификата.
3. HTTP-запросы успешно перенаправляются на HTTPS.
4. Реализованы псевдонимы для статических файлов.
5. Настроены виртуальные хосты для двух доменов.

---

## Заключение
Все задачи, поставленные в техническом задании, успешно выполнены.

