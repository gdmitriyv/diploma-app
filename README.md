# diploma-app

Тестовое приложение дипломного практикума: nginx отдаёт статическую страницу.

- `Dockerfile` — сборка образа на `nginx:1.27-alpine`
- `index.html`, `assets/` — статические файлы

Сборка локально:

    docker build -t diploma-app .
    docker run --rm -p 8080:80 diploma-app
