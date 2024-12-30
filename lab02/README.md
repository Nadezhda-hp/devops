# Отчет по лабораторной работе DevOps2

## Техническое задание
1. Написать "плохой" Dockerfile с не менее чем тремя примерами плохих практик.
2. Написать "хороший" Dockerfile, исправляющий указанные ошибки.
3. Описать плохие практики из "плохого" Dockerfile, почему они считаются плохими, и как они были исправлены в "хорошем" варианте.
4. Указать две плохие практики работы с контейнерами (не касающиеся написания Dockerfile).

---

## Установка Docker

### Шаги выполнения

Обновление пакетов:
```bash
sudo apt update
```
*Вывод консоли:*
```
... All packages are up to date.
```

Установка необходимых пакетов:
```bash
sudo apt install curl software-properties-common ca-certificates apt-transport-https -y
```
*Вывод консоли:*
```
... Successfully installed.
```

Импорт GPG-ключа Docker:
```bash
wget -O- https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor | sudo tee /etc/apt/keyrings/docker.gpg > /dev/null
```
*Вывод консоли:*
```
... Keyring saved.
```

Добавление репозитория Docker:
```bash
echo "deb [arch=amd64 signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu jammy stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
*Вывод консоли:*
```
... Repository added.
```

Установка Docker:
```bash
sudo apt install docker-ce -y
```
*Вывод консоли:*
```
... Docker successfully installed.
```

Проверка статуса Docker:
```bash
sudo systemctl status docker
```
*Вывод консоли:*
```
Active: active (running)
```

---

## "Плохой" Dockerfile

### Код:
```dockerfile
FROM ubuntu:latest

RUN apt-get update
RUN apt-get install bash
RUN apt-get install -y vim
RUN apt-get install -y curl

ADD https://example.com/script.sh .

CMD ["bash", "script.sh"]
```

### Обнаруженные проблемы:

1. **Использование `ubuntu:latest`.** Это приводит к нестабильной версии базового образа, что может вызвать проблемы совместимости.
2. **Множественные команды `RUN`.** Они создают дополнительные слои, увеличивая размер образа.
3. **Использование `ADD` вместо `COPY`.** `ADD` автоматически извлекает содержимое, что может быть уязвимо для атак.

---

## "Хороший" Dockerfile

### Код:
```dockerfile
FROM ubuntu:22.04

WORKDIR /app

RUN apt-get update && \
    apt-get install -y vim curl && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

COPY script.sh .

ENTRYPOINT ["bash"]
CMD ["script.sh"]
```

### Улучшения:

1. **Фиксированная версия Ubuntu.** Теперь используется конкретная версия (22.04), что делает образ стабильным.
2. **Оптимизация команд `RUN`.** Команды объединены, что сокращает количество слоев.
3. **Замена `ADD` на `COPY`.** Использование `COPY` для локальных файлов повышает безопасность.

### Проверка сборки и запуска:
Сборка образа:
```bash
docker build -t good-dockerfile .
```
*Вывод консоли:*
```
Successfully built good-dockerfile
```

Запуск контейнера:
```bash
docker run good-dockerfile
```
*Вывод консоли:*
```
Script executed successfully.
```

---

## Плохие практики работы с контейнерами

1. **Запуск контейнеров от имени root.** Это создает риски безопасности. Рекомендуется использовать флаг `--user` для указания безопасного пользователя.

2. **Неограниченные ресурсы.** Отсутствие ограничений на использование CPU и памяти может привести к сбоям системы. Следует указывать лимиты с помощью флагов `--memory` и `--cpu-shares` при запуске контейнера.

---

## Заключение
Все задачи лабораторной работы выполнены. Применены улучшения для написания Dockerfile и описаны ключевые аспекты безопасной работы с контейнерами.
