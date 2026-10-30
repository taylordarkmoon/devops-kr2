# KR2 template

Counter service on Flask + Redis.

Local run: `pip install -r requirements.txt && flask --app app run`

## Развёртывание на devops-vm (КР № 2, вариант 2)

| Параметр | Значение |
|---|---|
| Доменное имя | `notes.devops.local` |
| Порт приложения | `8002` (опубликован только на `127.0.0.1`) |
| Образ | `kr2-app:1.0` на базе `python:3.12-slim`, пользователь `app` |
| Хранилище | `redis:7-alpine`, пароль, AOF, том `redis_data` |

1. Доставка: `rsync -avz --delete --exclude=.env --exclude=.git ./ devops:kr2/`
2. Секрет на сервере (не коммитится, права 600):
   `(umask 077; echo "REDIS_PASSWORD=$(openssl rand -hex 32)" > ~/kr2/.env)`
3. Запуск: `cd ~/kr2 && docker compose up -d --build`
4. Публикация: `nginx/kr2.conf` → `/etc/nginx/sites-available/kr2`, ссылка в
   `sites-enabled`, сертификат `/etc/ssl/certs/kr2.crt` (SAN `DNS:notes.devops.local`),
   ключ `/etc/ssl/private/kr2.key` (600, root); `sudo nginx -t && sudo systemctl reload nginx`.
