---
layout: post
title: Deploying a django app - Part 1
subtitle: Using AWS for storing static files
image: /img/deploy_django_p1.png
tags: [programming, software, open source, web apps]
---

This is a two part post which will guide you to host your own django app in a production environment using **Amazon Web Services (AWS)** for *storing static files* and **Heroku** for *hosting the project*.

![Deploy Django part 1](/img/deploy_django_p1_img.png)

This part will cover the storing of **static files** in **AWS S3 Bucket**.

# AWS Setup

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
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:ListAllMyBuckets"
            ],
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
```
$ pip install boto boto3 django-storages
```

### 2. Update settings and migrate
- Add the app `storages` to the `INSTALLED_APPS` in `settings.py`
```
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
from *settings.py*