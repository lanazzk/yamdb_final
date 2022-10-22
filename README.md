![example workflow]([https://github.com/lanazzk/yamdb_final/tree/master/.github/workflows/yamdb_workflow.yml/badge.svg)
# API к сервису YaMDb

---

### Описание сервиса


Сервис **YaMDb** собирает **отзывы (Review)** пользователей на **произведения (Titles)**. Произведения делятся на категории: «Книги», «Фильмы», «Музыка». Список **категорий (Category)** может быть расширен администратором (например, можно добавить категорию «Изобразительное искусство» или «Ювелирка»).
Сами произведения в YaMDb не хранятся, здесь нельзя посмотреть фильм или послушать музыку.

В каждой категории есть произведения: книги, фильмы или музыка. Например, в категории «Книги» могут быть произведения «Винни-Пух и все-все-все» и «Марсианские хроники», а в категории «Музыка» — песня «Давеча» группы «Насекомые» и вторая сюита Баха.
Произведению может быть присвоен жанр (Genre) из списка предустановленных (например, «Сказка», «Рок» или «Артхаус»). Новые жанры может создавать только администратор.

Благодарные или возмущённые пользователи оставляют к произведениям текстовые отзывы (Review) и ставят произведению оценку в диапазоне от одного до десяти (целое число); из пользовательских оценок формируется усреднённая оценка произведения — рейтинг (целое число). На одно произведение пользователь может оставить только один отзыв.

### Документация к сервису находится по адресу (после установки и запуска):
`http://127.0.0.1:8000/redoc/`

### Режим администрирования находится по адресу:
`http://127.0.0.1:8000/admin/`

### Технологии
- Python 3.8
- Django 3.2
- Django Rest Framework
- Simple-JWT
- PostreSQL
- Nginx
- Gunicorn
- Docker

### Запуск проекта в Docker

Склонировать проект можно с:
```
git@github.com:lanazzk/infra_sp2.git
```
Установите и активируйте вирутальное окружение.

Зайдите в папку /infra

Разверните проект командой:
```
docker-compose up
```
Шаблон наполнения env-файла:
```
DB_ENGINE=django.db.backends.postgresql
DB_NAME=postgres
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres123
DB_HOST=db
DB_PORT=5432

```
Выполните по очереди команды (Миграции, создание суперюзераб собрать статику и загрузить данные из БД):

```
docker-compose exec web python manage.py migrate
docker-compose exec web python manage.py createsuperuser
docker-compose exec web python manage.py collectstatic --no-input
docker-compose exec web python manage.py loaddata fixtures.json
```

### Примеры запросов/ответов:
#### Регистрация нового пользователя:
Получить код подтверждения на переданный email.Поля email и username должны быть уникальными.
```
POST /auth/signup/
{
  "email": "string",
  "username": "string"
}
```
#### Запрос на получение токена авторизации:
Получение JWT-токена в обмен на username и confirmation code.
```
POST auth/token/
{
  "username": "Ваш логин",
  "confirmation_code": "Код подтверждения"
}
```
#### Ответ:

```json
{
  "token": "Токен для авторизации на сервисе"
}
```
#### Работа с API для пользователей:
Создать категорию.
```
Права доступа: Администратор. Поле slug каждой категории должно быть уникальным.
POST /categories/
json
{
  "name": "string",
  "slug": "string"
}
```
Удалить категорию.
```
Права доступа: Администратор.
DELETE /categories/{slug}
```
Получение списка всех произведений.
```
Права доступа: Доступно без токена
GET /titles/
[
  {
    "count": 0,
    "next": "string",
    "previous": "string",
    "results": [
      {
        "id": 0,
        "name": "string",
        "year": 0,
        "rating": 0,
        "description": "string",
        "genre": [
          {
            "name": "string",
            "slug": "string"
          }
        ],
        "category": {
          "name": "string",
          "slug": "string"
        }
```
Добавление нового отзыва
```
Права доступа: Аутентифицированные пользователи.
POST /titles/{title_id}/reviews/
{
  "text": "string",
  "score": 1
}
Ответ
{
  "id": 0,
  "text": "string",
  "author": "string",
  "score": 1,
  "pub_date": "2019-08-24T14:15:22Z"
}
```
Полный список можно посмотреть в документации.
`http://127.0.0.1:8000/redoc/`
