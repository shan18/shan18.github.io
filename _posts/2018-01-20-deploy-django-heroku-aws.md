---
layout: post
title: Deploying a Django app on Heroku
subtitle: Going live with Heroku and using AWS for storing static files
image: /img/deploy_django_heroku_aws/logo.png
tags: [programming, software, open source, web apps]
---

This is post will guide you to host your own django app in a production environment using **Amazon Web Services (AWS)** for _storing static files_ and **Heroku** for _hosting the project_.

![Django Heroku AWS logo](/img/deploy_django_heroku_aws/header.png)

# AWS Setup

AWS will be used to host the static files of the project.

### 1. Create an AWS Account [here](https://aws.amazon.com/).

### 2. Create a new S3 Bucket

- Navigate to S3 from [here](https://console.aws.amazon.com/s3/home).
- Click `Create Bucket`.
- Create a unique bucket name.
- Select region according to your Primary user's location. For reference, see: [AWS Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html#s3_region).
- Keep other settings as the default ones and create the bucket.

### 3. Create credentials for AWS User

- Navigate to [IAM Users](https://console.aws.amazon.com/iam/home?#users).
- Select `Create New Users`
- Enter the username.
- Ensure `Programmatic Access` is selected and hit `Next`.
- Select `Download credentials` and keep the `credentials.csv` file safe as it will be required later.

### 4. Add policies to your IAM user

#### Default policies

- Navigate to [IAM Home](https://console.aws.amazon.com/iam/home?#/users).
- Select user and click on `Permissions` tab.
- Click on `Attach Existing Policies Directly` and add any policies as per your requirement.

#### Custom Policies

- Navigate to [IAM Home](https://console.aws.amazon.com/iam/home?#/users).
- Select user and click on `Permissions` tab.
- Click on `Attach Existing Policies Directly` and select `Create Policy`.
- Go to the `JSON` tab and paste the policy given below. Change all `<your_bucket_name>` to the name of your bucket in S3 (set above). Do not change version date.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["s3:ListAllMyBuckets"],
      "Resource": "arn:aws:s3:::*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "s3:ListBucket",
        "s3:GetBucketLocation",
        "s3:ListBucketMultipartUploads",
        "s3:ListBucketVersions"
      ],
      "Resource": "arn:aws:s3:::<your_bucket_name>"
    },
    {
      "Effect": "Allow",
      "Action": [
        "s3:*Object*",
        "s3:ListMultipartUploadParts",
        "s3:AbortMultipartUpload"
      ],
      "Resource": "arn:aws:s3:::<your_bucket_name>/*"
    }
  ]
}
```

- The Actions that we choose to set are based on what we want this user to be able to do. The line `"s3:*Object*"`, will handle a lot of our permissions for handling objects for the specified bucket within the Recourse Value.

<br/>
# Django Setup

### 1. Install requirements

**boto** and **boto3** are python bindings for AWS. **django-storages** are used by django to send static files to AWS.

```bash
$ pip install boto boto3 django-storages
```

### 2. Update settings and migrate

- Add the app `storages` to the `INSTALLED_APPS` in `settings.py`

```python
INSTALLED_APPS = [
    ...
    'storages',
    ...
]
```

- Run migrations

```
$ python manage.py migrate
```

### 3. Set up the AWS module

- Create `aws` module in same directory as `settings.py`

```
$ pwd
/path/to/<your-project>/<main-app>/
$ ls
__init__.py settings.py wsgi.py urls.py
$ mkdir aws && cd aws
$ touch __init__.py
$ touch utils.py
$ touch conf.py
```

- In `utils.py` add the following

  ```
  from storages.backends.s3boto3 import S3Boto3Storage

  StaticRootS3BotoStorage = lambda: S3Boto3Storage(location='static')
  MediaRootS3BotoStorage  = lambda: S3Boto3Storage(location='media')
  ```

- Fetch the `Access Key Id` and `Secret Access Key` from `credentials.csv` downloaded earlier. Then in `conf.py` add the following

  ```
  import datetime
  AWS_ACCESS_KEY_ID = "<your_access_key_id>"
  AWS_SECRET_ACCESS_KEY = "<your_secret_access_key>"
  AWS_FILE_EXPIRE = 200
  AWS_PRELOAD_METADATA = True
  AWS_QUERYSTRING_AUTH = True

  DEFAULT_FILE_STORAGE = '<your-project>.aws.utils.MediaRootS3BotoStorage'
  STATICFILES_STORAGE = '<your-project>.aws.utils.StaticRootS3BotoStorage'
  AWS_STORAGE_BUCKET_NAME = '<your_bucket_name>'
  S3DIRECT_REGION = 'us-west-2'
  S3_URL = '//%s.s3.amazonaws.com/' % AWS_STORAGE_BUCKET_NAME
  MEDIA_URL = '//%s.s3.amazonaws.com/media/' % AWS_STORAGE_BUCKET_NAME
  MEDIA_ROOT = MEDIA_URL
  STATIC_URL = S3_URL + 'static/'
  ADMIN_MEDIA_PREFIX = STATIC_URL + 'admin/'

  two_months = datetime.timedelta(days=61)
  date_two_months_later = datetime.date.today() + two_months
  expires = date_two_months_later.strftime("%A, %d %B %Y 20:00:00 GMT")

  AWS_HEADERS = {
      'Expires': expires,
      'Cache-Control': 'max-age=%d' % (int(two_months.total_seconds()), ),
  }
  ```

### 4. Update settings.py

```
from <your-project>.aws.conf import *
```

### 5. Push the static files to S3 Bucket

```
$ python manage.py collectstatic
```

<br/>
## Note

- Any existing media files (before the AWS setup) cannot be uploaded to the bucket automatically, they have to be manually uploaded either from AWS website, Django admin or through aws-cli. For cli, see: [AWS CLI Setup](https://aws.amazon.com/getting-started/tutorials/backup-to-s3-cli/)  
  Once AWS has been setup, all media files will be directly uploaded to AWS bucket.
- For testing, to disable the AWS upload and use local static files, comment out  
  `from <your-project>.aws.conf import *`  
  from _settings.py_

<br/>
# Heroku Setup

Now it's time to host the project on heroku. All the files that are created and the shell commands given below should be executed within the **root** of the **project directory** i.e. the directory containing the file `manage.py`

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
   $ heroku certs:auto:enable
   ```

<br/>
## Note

- When changes are made to the models, run `makemigrations` locally first, then push the changes to heroku and then run `migrate` on the production server. (This is because, if any error occurs in models, then we can fix it locally first.)
- Whenever some changes are made to the environment variables in herkou, restart the server from `More --> Restart all dynos`
