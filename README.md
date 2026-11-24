## Шаблон для третьего ДЗ по ИПР

Условие дз доступно по [ссылке](https://github.com/Nikfilk2030/missing_semester_hse_cs/blob/main/2026/amcs/year_1/hw_3/README.md).

## Запуск одного контейнера

docker build -t counter-app:1 .
docker run -d --name counter-1 -p 127.0.0.1:8080:8000 -e GREETING="Hello from container!" counter-app:1
curl [http://127.0.0.1:8080/](http://127.0.0.1:8080/)
curl -X POST [http://127.0.0.1:8080/increment](http://127.0.0.1:8080/increment)
curl [http://127.0.0.1:8080/](http://127.0.0.1:8080/)
docker logs counter-1
docker stop counter-1 && docker rm counter-1

## Запуск через Docker Compose

cp .env.example .env
docker compose up -d --build
curl -fsS [http://127.0.0.1:8080/](http://127.0.0.1:8080/)
curl -fsS -X POST [http://127.0.0.1:8080/increment](http://127.0.0.1:8080/increment)
curl -fsS [http://127.0.0.1:8080/](http://127.0.0.1:8080/)
docker compose rm -sf app
docker compose up -d
curl -fsS [http://127.0.0.1:8080/](http://127.0.0.1:8080/)

## Настройки

- `APP_PORT` — порт на хосте, пробрасываемый на `8000` в контейнере (по умолчанию `8080`).
- `GREETING` — текст приветствия в поле `greeting` (по умолчанию `Hello from Compose!`).
- `counter-data` — именованный volume, подключённый к `/data` для сохранения базы SQLite `/data/counter.db`.

## Результаты проверок

- В `compose.yaml` настроены сборка образа, проброс `${APP_PORT:-8080}:8000`, переменная `GREETING` и volume `counter-data:/data`.
- В логах контейнера при запуске фиксируется `listening port=8000 storage=sqlite`.
- После двух `POST /increment` значение счётчика стало `2` (`{"greeting": "Hello from Compose!", "count": 2, "hostname": "42fd4af15df5", "storage": "sqlite"}`).
- После удаления и повторного создания контейнера значение счётчика сохранилось (`{"greeting": "Hello from Compose!", "count": 2, "hostname": "15a3ef082c4d", "storage": "sqlite"}`).
- При изменении в `.env` на `APP_PORT=8081` и `GREETING=Custom greeting` и перезапуске через `docker compose up -d` ответ по `[http://127.0.0.1:8081/](http://127.0.0.1:8081/)` вернул `{"greeting": "Custom greeting", "count": 2, "hostname": "366f346cd4e5", "storage": "sqlite"}`.
