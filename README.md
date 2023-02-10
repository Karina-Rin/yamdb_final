![yamdb_workflow](https://github.com/Karina-Rin/yamdb_final/workflows/yamdb_workflow.yml/badge.svg)

# CI и CD проекта api_yamdb
## Описание проекта
Проект _YaMDb_ собирает отзывы и комментарии пользователей на произведения.

Произведения делятся на категории, такие как `«Книги»`, `«Фильмы»`, `«Музыка»`. 

Благодарные или возмущённые пользователи оставляют к произведениям текстовые 
отзывы и ставят произведению оценку в диапазоне от одного до десяти (целое 
число); из пользовательских оценок формируется усреднённая оценка произведения 
— рейтинг (целое число). На одно произведение пользователь может оставить 
только один отзыв.

Пользователи могут оставлять комментарии к отзывам.

Сами произведения в YaMDb не хранятся, здесь нельзя посмотреть фильм или 
послушать музыку.

Позволяет делать запросы к БД с любого устройства.
Проект упакован в Docker контейнеры.

## Workflow
* tests - Проверка кода на соответствие стандарту PEP8 (только свой код, код 
фреймворка Django не трогаем).
* build_and_push_to_docker_hub - Автоматическая пересборка и пуш обновлённого 
образа на Docker Hub.
* deploy - Автоматический деплой проекта на боевой сервер.
* send_message - Отправка оповещения об успешном завершении процесса в 
Telegram_bot.

## Запуск приложения

Клонируем репозиторий:
```
git clone https://github.com/Karina-Rin/yamdb_final.git
```

Создаем и активируем виртуальное окружение, обновляем pip:
```
python3 -m venv venv
. venv/bin/activate
python3 -m pip install --upgrade pip
```

Устанавливаем зависимости:
```
pip install -r requirements.txt
```
### Шаблон наполнения env-файла
```
Создайте `.env` файл с переменными окружения для работы с базой данных в 
директории `infra/` по примеру файла `.env.sample`
```

## Подготавливаем репозиторий на GitHub

В репозитории на GitHub прописываем Secrets:
```
DOCKER_USERNAME - имя пользователя DockerHub
DOCKER_PASSWORD - пароль пользователя DockerHub
HOST - IP сервера
USER - текущий пользователь
SSH_KEY - приватный ssh-ключ (начало -----BEGIN OPENSSH PRIVATE KEY----- ... -----END OPENSSH PRIVATE KEY----- конец)
PASSPHRASE - кодовая фраза для ssh-ключа (если ваш ssh-ключ защищён фразой-паролем)
TELEGRAM_TO - ID своего телеграм-аккаунта. Узнать можно у бота @userinfobot
TELEGRAM_TOKEN - токен вашего бота. Получить можно у бота @BotFather
```

### Развертывание приложения на боевом сервере

1. Пушим проект на DockerHub. 
При пуше в ветку main/master приложение пройдет тесты, обновит образ и сделает деплой на сервер.

2. Подлючаемся к боевому серверу:
```
ssh <USER>@<HOST>
```
3. Переходим в запущенный контейнер приложения:
```
docker container exec -it <CONTAINER ID> bash
```
4. Внутри контейнера собираем статику и выполняем миграции:
```
docker-compose exec web python manage.py collectstatic --no-input
docker-compose exec web python manage.py makemigrations
docker-compose exec web python manage.py migrate --noinput
```

5. Наполняем БД начальными тестовыми данными:
```
python3 manage.py shell
>>> from django.contrib.contenttypes.models import ContentType
>>> ContentType.objects.all().delete()
>>> quit()
python manage.py loaddata fixtures.json
```

6. Создаем суперпользователя:
```
docker-compose exec web python manage.py createsuperuser
```

### Адрес развернутого проекта

http://62.84.120.208/redoc/


## Авторы
Karina-Rin