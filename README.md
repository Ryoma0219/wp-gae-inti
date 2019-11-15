<div align="center">
  <H2>WP on GAE template</H2>
</div>

### Base repository
https://github.com/GoogleCloudPlatform/php-docs-samples

Created By this command on base repo.
```
cd php-docs-samples/appengine/php72/wordpress
composer install
php vendor/bin/wp-gae create
```

### Setups
*This is necessary in advance*
1. Regist Google Cloud Platform and enable billing
2. Install Cloud SDK (`gcloud` command)


*You can also step on Google Cloud Console GUI.*

**Create instance on Cloud SQL**
You can choose every tier.
```
gcloud sql instances create ${INSTANCE_NAME} \
    --activation-policy=ALWAYS \
    --tier=db-n1-standard-1
```

**Create Database on instance you just created**
```
gcloud sql databases create ${DATABASE_NAME} --instance ${INSTANCE_NAME}
```

**Set Root Password**
```
gcloud sql users set-password root \
    --host=% \
    --instance ${INSTANCE_NAME} \
    --password=${PASSWORD}
```

### Local Development Setup
1. Run Cloud SQL Proxy
*Install cloud_sql_proxy is necessary in advance*
```sh
sudo /cloud_sql_proxy \
    -dir /cloudsql \
    -instances=${YOUR_PROJECT_ID}:us-central1:${INSTANCE_NAME} \
    -credential_file=secrets/${SERVICE_ACCOUNT}.json
```

2. Change Config File
```
cp wp-config-sample.php ./wp-config.php
```

Correct for your settings `PROJECT_ID` `INSTANCE_NAME` `ROOT_PASSWORD`
```php
// ** MySQL settings - You can get this info from your web host ** //
if ($onGae) {
    /** The name of the Cloud SQL database for WordPress */
    define('DB_NAME', '${DATABASE_NAME}');
    /** Production login info */
    define('DB_HOST', ':/cloudsql/${PROJECT_ID}:us-central1:${INSTANCE_NAME}');
    define('DB_USER', 'root');
    define('DB_PASSWORD', '${ROOT_PASSWORD}');
} else {
    /** The name of the local database for WordPress */
    define('DB_NAME', '${DATABASE_NAME}');
    define('DB_HOST', ':/cloudsql/${PROJECT_ID}:us-central1:${INSTANCE_NAME}');
    define('DB_USER', 'root');
    define('DB_PASSWORD', '${ROOT_PASSWORD}');
}
```

3. Run (Need 1. Run Cloud SQL Proxy)
```
php -S localhost:8000
```

### Deploy
```
gcloud app deploy app.yaml --version
```