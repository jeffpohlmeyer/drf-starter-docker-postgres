# drf-starter-docker-postgres
A starter project for a django rest framework backend with user auth using Djoser

## Motivation
This was designed because there seems to be no decent Docker-based Django Rest Framework + Postgres + Celery + Redis repo/tutorial out there.  Everything seems very specific to certain use-cases and as I don't know a lot about Docker this was my first foray into using Docker.  I pulled a very specific setup of the [django-cookiecutter](https://github.com/pydanny/cookiecutter-django "Django Cookiecutter") install, using the following:
- windows: y
- use_pycharm: y
- use_docker: y
- postgresql_version: 12.3
- js_task_runner: None
- cloud_provider: None
- mail_service: Mailgun
- use_async: n
- use_drf: y
- custom_bootstrap_compilation: n
- use_compressor: n
- use_celery: y
- use_mailhog: n
- use_sentry: y
- use_whitenoise: y
- use_heroku: n
- ci_tool: None
- keep_local_envs_in_vcs: y
- debug: n

I really like the setup of pydanny's cookiecutter setup, with separate files for local and production, a separated settings/requirements setup, separation of concerns for the different supporting technologies, etc.  I wanted to know enough Docker to know what's going on but my goal was not to learn Docker; it was to create a production-ready Django API.

After setting up the project I then stripped out all of the allauth functionality and the templating functionality, customized the user model using a methodology similar to how is described in many places for setting up a custom user model.  I then looked into using dj-rest-auth (which requires inclusion of allauth if you want to have registration) and drf-social-oauth2, both of which didn't provide what I needed, which is simple username or email + password authentication with some eventual social auth.  For this, [Djoser](https://github.com/sunscrapers/djoser) seems the obvious solution.  After setting up Djoser with the settings I needed I wanted to add some styling to the emails that get sent out so I created custom templates and a custom email.py file to handle it.
The goal is to eventually fork the main django cookiecutter repo and create one designed solely as a production-ready DRF-based app instead of the full-stack app.  That will take a little time, though, and I needed this much sooner so this is what I get right now.

## Env files
After cloning the repo you'll need to create the following setup at the project root level:

```
.
├── .envs
    ├── .local
        ├── .django
        ├── .postgres        
    ├── .production
        ├── .django
        ├── .postgres        
```
where each file will need the following variables set
- .envs -> .local -> .django
  - REDIS_URL -> typically redis://redis:6379/0
  - CELERY_FLOWER_USER
  - CELERY_FLOWER_PASSWORD
  - SITE_NAME -> used for generating emails
  - SITE_DOMAIN -> used for generating emails, frontend domain
  - SITE_URL -> used for generating emails, full frontend url
- .envs -> .local -> .postgres
  - POSTGRES_HOST
  - POSTGRES_PORT
  - POSTGRES_DB
  - POSTGRES_USER
  - POSTGRES_PASSWORD
- .envs -> .production -> .django
  - DJANGO_SETTINGS_MODULE=config.settings.production
  - DJANGO_SECRET_KEY
  - DJANGO_ADMIN_URL -> NOT admin/
  - DJANGO_ALLOWED_HOSTS -> production server url
  - DJANGO_SECURE_SSL_REDIRECT -> False
  - DJANGO_SERVER_EMAIL
  - MAILGUN_API_KEY
  - MAILGUN_DOMAIN
  - WEB_CONCURRENCY=4
  - SENTRY_DSN
  - REDIS_URL -> typically redis://redis:6379/0
  - CELERY_FLOWER_USER
  - CELERY_FLOWER_PASSWORD
- .envs -> .production -> .postgres (these should ideally be different than the ones in .local)
  - POSTGRES_HOST
  - POSTGRES_PORT
  - POSTGRES_DB
  - POSTGRES_USER
  - POSTGRES_PASSWORD

## Last Steps
After cloning the repo, you can leave the name as is (drf-starter-docker-postgres) and just incorporate it as the "server" folder in your frontend and backend stack or you can do a find and replace on drf-starter-docker-postgres to whatever you want to call your project.
As I mentioned, I will eventually fork the django-cookiecutter project which will make this more modular but for now this is going to have to suffice.
