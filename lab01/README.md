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
```bash
brew install nginx
```

#### Проверка установки:
```bash
nginx -v
```
*Вывод консоли:*
```
nginx version: nginx/1.21.6
```

---

### 2. Настройка HTTPS

#### Генерация самоподписанного сертификата:
```bash
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /usr/local/etc/nginx/server.key -out /usr/local/etc/nginx/server.crt
```
*Вывод консоли:*
```
Generating a RSA private key
.....................................................+++++
....................................+++++
writing new private key to '/usr/local/etc/nginx/server.key'
-----
```
*Заполненные данные:*
- Country Name: RU
- State: Moscow
- Locality: Moscow
- Organization: MyLab
- Common Name: localhost

#### Настройка конфигурации Nginx:
В файле `/usr/local/etc/nginx/nginx.conf` добавлен следующий блок для HTTPS:
```nginx
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
```
*Вывод консоли при проверке конфигурации:*
```bash
nginx -t
```
```
nginx: the configuration file /usr/local/etc/nginx/nginx.conf syntax is ok
nginx: configuration file /usr/local/etc/nginx/nginx.conf test is successful
```

---

### 3. Принудительное перенаправление HTTP на HTTPS

#### Добавлено в конфигурацию:
```nginx
server {
    listen 80;
    server_name localhost;

    return 301 https://$host$request_uri;
}
```
*Проверка:*
```bash
curl -I http://localhost
```
*Вывод консоли:*
```
HTTP/1.1 301 Moved Permanently
Location: https://localhost/
```

---

### 4. Настройка псевдонимов (alias)

#### Добавлено:
```nginx
location /static/ {
    alias /usr/local/var/www/static/;
}
```
Создан каталог и файл:
```bash
mkdir -p /usr/local/var/www/static
echo "Alias test" > /usr/local/var/www/static/test.html
```
*Проверка:*
```bash
curl https://localhost/static/test.html
```
*Вывод консоли:*
```
Alias test
```

---

### 5. Настройка виртуальных хостов

#### Добавлено:
Для домена `example1.local`:
```nginx
server {
    listen 443 ssl;
    server_name example1.local;

    ssl_certificate /usr/local/etc/nginx/server.crt;
    ssl_certificate_key /usr/local/etc/nginx/server.key;

    root /usr/local/var/www/example1;
    index index.html;
}
```
Для домена `example2.local`:
```nginx
server {
    listen 443 ssl;
    server_name example2.local;

    ssl_certificate /usr/local/etc/nginx/server.crt;
    ssl_certificate_key /usr/local/etc/nginx/server.key;

    root /usr/local/var/www/example2;
    index index.html;
}
```

#### Созданы каталоги и файлы:
```bash
mkdir -p /usr/local/var/www/example1
mkdir -p /usr/local/var/www/example2

echo "Example 1" > /usr/local/var/www/example1/index.html
echo "Example 2" > /usr/local/var/www/example2/index.html
```
*Проверка локальных доменов:*
Файл `/etc/hosts` дополнен:
```
127.0.0.1 example1.local
127.0.0.1 example2.local
```
*Выводы консоли:*
```bash
curl https://example1.local
```
```
Example 1
```
```bash
curl https://example2.local
```
```
Example 2
```

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
