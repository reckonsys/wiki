# Wiki

Wiki

[Checkout template repositories](https://github.com/orgs/reckonsys/repositories?type=template) 
 
## Tools

Before running any backend / frontend code, please make sure that the following services are all installed:

* [PostgreSQL](https://www.postgresql.org/)
* [MailHog](https://github.com/mailhog/MailHog)
* [Minio](https://min.io/)
* [Redis](https://redis.io/)
* [Keycloak](https://www.keycloak.org/) [not available on Homebrew]
* [Docker](https://www.docker.com/) Only if you need to build and deploy

The recommeded way to install these tools is to use [Homebrew](https://brew.sh/) if you are using linux / Mac. If you are using Windows, Use a Ubuntu WSL and Homebrew. If your Windows version does not have WSL, it is strongly recommended to switch to Linux (or less ideally, a Windows version that supports WSL).

With that being said, KeyCloak is not available on HomeBrew. You can docnload that from [Keycloak's GitHub releases page](https://github.com/keycloak/keycloak/releases) maually and extract the archive into a folder.

```sh
brew install mailhog postgresql redis minio docker docker-compose docker-machine
```

### MailHog

Start the MailHog server using the command `MailHog` on your terminal.

To easly test and verify the email related functionalities in development time, we use MailHog which is a local SMTP server that can display emails. Once the mailhog server is started on your machine locally, You can check emails on mailhog by visiting [http://0.0.0.0:8025](http://0.0.0.0:8025).

The Django's [settings.py](https://github.com/reckonsys/backend/blob/develop/backend/settings.py) defaults to MailHog's SMTP settings

```py
EMAIL_HOST = env("EMAIL_HOST", "0.0.0.0")
EMAIL_PORT = int(env("EMAIL_PORT", "1025"))
EMAIL_HOST_USER = env("EMAIL_HOST_USER", "MailHog")
EMAIL_HOST_PASSWORD = env("EMAIL_HOST_PASSWORD", "MailHog")
EMAIL_USE_TLS = env("EMAIL_USE_TLS", "0") == "1"
EMAIL_USE_SSL = env("EMAIL_USE_SSL", "0") == "1"
EMAIL_BACKEND = "django.core.mail.backends.smtp.EmailBackend"
```

### Minio

Start the minio server using the command `minio server ~/data/minio` on your terminal. if the `~/data/minio` folder exists, please create it manually. 

Typically, AWS S3 (or DigitalOcean Spaces / Google Cloud Storage) for file management (unloading/downloading). In order to mock S3 locally, we use Minio. Once minio server is started on your machine locally, you can access the minio console by visiting [http://0.0.0.0:43705](http://0.0.0.0:43705). `minioadmin` is both the default username and password. `minioadmin` is also both the default access key id and secret.

The Django's "AWS Bucket" [settings.py](https://github.com/reckonsys/backend/blob/develop/backend/settings.py) defaults to minio settings

```py
AWS_ACCESS_KEY_ID = env("AWS_ACCESS_KEY_ID", "minioadmin")
AWS_SECRET_ACCESS_KEY = env("AWS_SECRET_ACCESS_KEY", "minioadmin")
AWS_BUCKET_NAME = env("AWS_BUCKET_NAME", "backend-local")
AWS_REGION = env("AWS_REGION", default="us-east-1")
AWS_BUCKET_REGION = env("AWS_BUCKET_REGION", "us-east-1")
if env("USE_AWS_S3") is None:
    # Use minio
    AWS_S3_ENDPOINT_URL = "http://0.0.0.0:9000"
else:
    # Use AWS S3
    AWS_S3_ENDPOINT_URL = None
```

### Redis

During development we may use redis as celery broker / for socket server. But during deployment we may choose to use AWS SQS / RabbitMQ for celery broker.

### Keycloak

start the keycloak server using the command `bin/kc.sh start-dev --spi-theme-static-max-age=-1 --spi-theme-cache-themes=false --spi-theme-cache-templates=false` from the folder when you extracted it.

Keycloak is our default authentication service (as we follow microservices architecture).  It is simialr to AWS Cognoito / Auth0 / Okta. 

Once the keycloak server is started, you can access it by visiting [http://0.0.0.0:8080](http://0.0.0.0:8080).

Be sure to use the correct public key in your [settings.py](https://github.com/reckonsys/backend/blob/develop/backend/settings.py) from the keycloak's realm that you are using.

```py
KEYCLOAK_PUBLIC_KEY = env(
    "KEYCLOAK_PUBLIC_KEY",
    "MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA4SvPRqJJA5O//iTg8/+BiCOZB2c0lK8TQ8plfem3md+OFhNC0d21Uzq8PGOSP/BrU/xeqg0DHvlzcriOTC0ZwceP0isIjUNHz1+sRRVPt69b/+HM331IGkuqTs4qk76ExTD4IMZ3nv0GHsKHZCOanRTSbqQTMaDaW3casXkFQyOYhyEbBu3atFZ+vWtMUkgFJ9wgHSOhWdJkX2JxzR65y/BgiJUtocn7YprKLxEKjHk4b+gLGPE017O81ooInbgH2XcZjxsG/S3Rpw4TSOh/6mpBCYb1YTYT3GzpVCoQ5K+AzEoo+eR+jp1zhe4GgZWLHpPgkKoOVvO7m3+FPXHvWwIDAQAB",  # noqa:E501
)
```
