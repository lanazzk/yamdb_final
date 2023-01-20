![build status](https://github.com/lanazzk/yamdb_final/actions/workflows/yamdb_workflow.yml/badge.svg)
# API to YaMDb service 

---

### Service description

This service YaMDb collects users' reviews of different works. The works are divided into categories: "Books", "Films", "Music" and genres "Fairy tale", "Rock", and "Arthouse". The lists of categories and genres can be expanded by the administrator.
Additionally configured CI/CD(Continuous Integration and Continuous Deployment) for this project:
  - automatic start of tests(pep8, pytest),
  - building or updating a docker image in the container on Docker Hub; 
  - automatic deployment to the production server;
  - sending a notification to Telegram that the deployment process has completed successfully

### Technologies
- Python 3.8
- Django 3.2
- Django Rest Framework
- Simple-JWT
- PostreSQL
- Nginx
- Gunicorn
- Docker

### How to launch a project:

Clone the repository and go to it on the command line:
```
git@github.com:lanazzk/infra_sp2.git
```
Create and activate a virtual environment:
```
python3 -m venv venv
source venv/Scripts/activate
```
Then go to the yamdb_final/infra folder and create there a .env file with environment variables needed for the application to work.
```
cd /infra
```
Env-file filling template:
```
DB_ENGINE=django.db.backends.postgresql
DB_NAME=postgres
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres123
DB_HOST=db
DB_PORT=5432
```

The next step is to run docker-compose:
```
docker-compose up

```
Run next commands:

```
docker-compose exec web python manage.py migrate
docker-compose exec web python manage.py createsuperuser
docker-compose exec web python manage.py collectstatic --no-input
docker-compose exec web python manage.py loaddata fixtures.json
```
After that the project should be available at http://localhost/.

### Examples of requests:
The full list of possible requests and responses can be seen after installing and running the API on the local server

`http://127.0.0.1:8000/redoc/`

#### New user registration :
```
POST /auth/signup/
{
  "email": "string",
  "username": "string"
}
```
#### Get authorization JWT-tocken:
```
POST auth/token/
{
  "username": "login",
  "confirmation_code": "confirmation code"
}
```
#### Response:

```json
{
  "token": "Token swdsgdjsdhh345dchj"
}
```
Creating new category.
```
Permissions class: Administrator. Slug required be unique.
POST /categories/
json
{
  "name": "string",
  "slug": "string"
}
```
Removing category
```
Permissions class: Administrator.
DELETE /categories/{slug}
```
Get lists of titles:
```
Permissions class: Available without token.
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
Adding new review
```
Permissions class: Authenticated users.
POST /titles/{title_id}/reviews/
{
  "text": "string",
  "score": 1
}
Response
{
  "id": 0,
  "text": "string",
  "author": "string",
  "score": 1,
  "pub_date": "2019-08-24T14:15:22Z"
}
```
