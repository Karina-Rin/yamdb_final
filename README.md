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

Переходим в директорию infra:
```
cd infra
```

Запускаем сборку контейнеров:

```
docker-compose up -d --build
```
Выполняем миграции: 

```
docker-compose exec web python manage.py migrate
```

Создаем суперпользователя:

```
docker-compose exec web python manage.py createsuperuser
```
Собираем статику:

```
docker-compose exec web python manage.py collectstatic --no-input
```

Описание команды для заполнения базы данными
```
docker-compose exec web python manage.py loaddata fixtures.json 
```

## Авторы
Karina-Rin