# Django Site

Докеризированный сайт на Django для экспериментов с Kubernetes.

Внутри контейнера Django приложение запускается с помощью Nginx Unit, не путать с Nginx. Сервер Nginx Unit выполняет сразу две функции: как веб-сервер он раздаёт файлы статики и медиа, а в роли сервера-приложений он запускает Python и Django. Таким образом Nginx Unit заменяет собой связку из двух сервисов Nginx и Gunicorn/uWSGI. [Подробнее про Nginx Unit](https://unit.nginx.org/).


## Установка контейнера в миникуб кластере:

### Установить миникуб:
```
minikube start --driver=driver --cpus=x --memory=xgb
minikube addons enable ingress
```

### Запустить деплойменты

Заполнить необходимые значения в secret.yml, упаковав их в base64

```
kubectl apply -f secret.yml
kubectl apply -f k8s-web.yml
kubectl apply -f k8s-ingress.yml
kubectl apply -f k8s-svc.yml
```

### Установить хелм постгреса

Заполнить файл pg.yml нужными значениями 

```
helm install my-release oci://registry-1.docker.io/bitnamicharts/postgresql -f pg.yml
```

### Получить айпи адрес ингресса

```
kubectl get ingress
```
Задать это значение в /etc/hosts как sb.test

Запустить удаление сессий джанго в кроне
```
kubectl apply -f clearsessions
```

## Переменные окружения

Образ с Django считывает настройки из переменных окружения:

`SECRET_KEY` -- обязательная секретная настройка Django. Это соль для генерации хэшей. Значение может быть любым, важно лишь, чтобы оно никому не было известно. [Документация Django](https://docs.djangoproject.com/en/3.2/ref/settings/#secret-key).

`DEBUG` -- настройка Django для включения отладочного режима. Принимает значения `TRUE` или `FALSE`. [Документация Django](https://docs.djangoproject.com/en/3.2/ref/settings/#std:setting-DEBUG).

`ALLOWED_HOSTS` -- настройка Django со списком разрешённых адресов. Если запрос прилетит на другой адрес, то сайт ответит ошибкой 400. Можно перечислить несколько адресов через запятую, например `127.0.0.1,192.168.0.1,site.test`. [Документация Django](https://docs.djangoproject.com/en/3.2/ref/settings/#allowed-hosts).

`DATABASE_URL` -- адрес для подключения к базе данных PostgreSQL. Другие СУБД сайт не поддерживает. [Формат записи](https://github.com/jacobian/dj-database-url#url-schema).
