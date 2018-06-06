---
layout: post
title: Deploying a django app - Part 2
subtitle: Going live with Heroku
image: /img/deploy_django_p2.jpg
tags: [programming, software, open source, web apps]
---

This is the **Part 2** of the post **Deploying a django app**. For **Part 1** go [here](/2018-01-20-deploy-django-p1). This part will cover steps required to host your django project on **Heroku**.

![Deploy django part 2](/img/deploy_django_p2_img.png)

All the files that are created and the shell commands given below should be executed within the **root** of the **project directory** i.e. the directory containing the file `manage.py`

1. Create a Heroku Account [here](https://www.heroku.com/).

2. Download Heroku cli from [here](https://devcenter.heroku.com/articles/heroku-cli).

3. Setup git
    ```
    $ git init
    $ git status
    <your-project>/
    manage.py
    ...
    ```

4. Install requirements
    ```
    $ pip install psycopg2 dj-database-url gunicorn
    ```

5. Create `runtime.txt` and write into it the python version of the project (let the version be Python 3.6.4)
    ```
    $ echo "python-3.6.4" > runtime.txt
    ```

6. Create `.gitignore` and `Procfile`
    ```
    $ echo ".py[cod]" > .gitignore
    $ echo "web: gunicorn <your_project>.wsgi" > Procfile
    ```
    Replace `<your_project>` with your project name or where the `.wsgi` file exists.  

7. Login to heroku
    ```
    $ heroku login
    ```
    Then enter the credentials.

8. Create Heroku Project:
    ```
    $ heroku create <project_name>
    ```

9. Provision Database Add-on:
    ```
    $ heroku addons:create heroku-postgresql:hobby-dev
    ```

10. Update `settings.py` to include:
    ```
    DEBUG = False
    ALLOWED_HOSTS =  ['project-name.herokuapp.com', '.yourdomain.com']
    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.sqlite3',
            'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
        }
    }

    # add this
    import dj_database_url
    db_from_env = dj_database_url.config()
    DATABASES['default'].update(db_from_env)
    DATABASES['default']['CONN_MAX_AGE'] = 500
    ```

11. Setup Environment Variables
    - Go to your app's Heroku dashboard and navigate to `Settings`.
    - Click on `Reveal Config Vars`
    - Add all the security keys of your project into it. (For example the `SECRET_KEY`, `EMAIL_HOST_PASSWORD` etc.)
    - Add the key values without any quotes.

12. Update `requirements.txt`
    ```
    $ pip freeze > requirements.txt
    $ git add requirements.txt
    $ git commit -m "Updated requirements.txt"
    ```

13. Commit & Push
    ```
    $ git add .
    $ git commit -m "Initial Heroku commit"
    $ git push heroku master
    ```

14. Disable Collectstatic in order to use S3 for static files
    ```
    $ heroku config:set DISABLE_COLLECTSTATIC=1
    ```

15. Run migrations
    ```
    $ heroku run python manage.py migrate
    ```

16. Other common commands:
    - Create superuser: `heroku run python manage.py createsuperuser`
    - Enter Heroku's bash: `heroku run bash` for shell access
    - Live Python-Django shell: `heroku run python manage.py shell`


# Custom Domains & SSL on Heroku

1. Add a Custom Domain
    ```
    $ heroku domains:add <your-custom-domain.com>
    $ heroku domains:add www.<your-custom-domain.com>
    ```

2. Update your DNS to what heroku says above

3. Enable Heroku to Handle Let's Encrypt Certificate
    ```
    heroku certs:auto:enable
    ```

<br/>
## Note

- When changes are made to the models, run `makemigrations` locally first, then push the changes to heroku and then run `migrate` on the production server. (This is because, if any error occurs in models, then we can fix it locally first.)
- Whenever some changes are made to the environment variables in herkou, restart the server from `More --> Restart all dynos`
