# diploma-app

Тестовое приложение дипломного практикума DevOps: nginx отдаёт статическую страницу.
Репозиторий инфраструктуры и конфигурации кластера: [diploma_work](https://github.com/gdmitriyv/diploma_work).

## Состав

| Файл | Назначение |
|---|---|
| `Dockerfile` | Образ на `nginx:1.27-alpine`, копирует страницу и картинки |
| `index.html` | Страница; вместо версии стоит подстановка `__VERSION__` |
| `assets/` | Картинки страницы |
| `.github/workflows/ci.yml` | CI/CD в GitHub Actions |

## Как работает CI/CD

| Событие | Что происходит |
|---|---|
| Коммит в `main` | Собирается образ и отправляется в Yandex Container Registry с тегом `sha-<7 символов коммита>` |
| Тег `v*` (например, `v1.2.0`) | Собирается образ с тегом `v1.2.0`, отправляется в реестр и выкатывается в кластер: `kubectl set image` и ожидание завершения раскатки |

В страницу версия подставляется автоматически: шаг `Put version into the page` заменяет `__VERSION__` на тег образа.

Образ: `cr.yandex/<ID реестра>/nginx-app:<тег>`.

## Что нужно настроить в репозитории если повторно будет редеплой инфраструктуры

Settings, Secrets and variables, Actions:

| Тип | Имя | Значение |
|---|---|---|
| Variable | `REGISTRY_ID` | ID реестра `diploma-registry` (`terraform output container_registry_id`) |
| Secret | `YC_CI_KEY` | JSON-ключ сервисного аккаунта с ролью отправки образов (`terraform output -raw ci_registry_key`) |
| Secret | `KUBE_CONFIG` | kubeconfig аккаунта `ci-deployer` в base64 |

Подробная инструкция по получению значений находится в [diploma_work](https://github.com/gdmitriyv/diploma_work), раздел «CI/CD приложения».

<img width="1813" height="951" alt="14 action git diploma app" src="https://github.com/user-attachments/assets/7aabbfc7-dc04-4f85-8843-b19d9d38a2df" />
<img width="1426" height="839" alt="11  app up" src="https://github.com/user-attachments/assets/69246443-d8fb-469a-8891-01cc334252b9" />


## Выпуск версии

```bash
git add .
git commit -m "Описание изменений"
git push origin main
git tag v1.2.0
git push origin v1.2.0

