# testjose

This repo contains all the code of the website from https://resum.me

### Development

Uses the default Django development server.

1. Check that you have your .env.dev and .env.dev.db.  If for some reason these files are not generated, remember that you can find samples in the repository.
   ```sh
   $ cat .env.dev
   
   DEBUG=1
   SECRET_KEY=Plea$eUse$trong$ecretKey
   DJANGO_ALLOWED_HOSTS=localhost 127.0.0.1 [::1]
   SQL_ENGINE=django.db.backends.postgresql
   SQL_DATABASE=testjose_db_dev
   SQL_USER=testjose_user
   SQL_PASSWORD=testjose_password
   SQL_HOST=db
   SQL_PORT=5432
   DATABASE=postgres
   
   $ cat .env.dev.db

   POSTGRES_USER=testjose_user
   POSTGRES_PASSWORD=testjose_password
   POSTGRES_DB=testjose_db_dev
    ```

2. Create docker volumes to save the data:
    ```sh
    $ docker volume create --name=testjose_postgres_data_dev
    $ docker volume create --name=testjose_grafana
    ```

3. Build the images and run the containers:

    ```sh
    $ docker-compose up -d --build
    ```

    Test it out at [http://localhost:8000](http://localhost:8000). The "app" folder is mounted into the container and your code changes apply automatically.
    Grafana dashboard will be accesible in [http://localhost:3000](http://localhost:3000).

### Before deploying to production
It's important that the DNS records are setup properly before deploying the app to the production server. You will need to make sure that you have the following records:
- A: record to set the IPv4 of your production server
- AAAA: record to set the IPv6 of your production server
- CNAME: record to configure the "wwww" subdomain.

Here is an example of how to configure this in [GoDaddy](https://godaddy.com/)
![Screenshot from 2021-07-11 19-28-45](https://user-images.githubusercontent.com/17761956/125204570-40738000-e27e-11eb-81a4-7a495949af73.png)


### Production configuration

The application will be served using Gunicorn and NGINX with HTTPS certificates.

1. Check that you have your .env.prod.grafana, .env.prod, .env.prod.db and .env.prod.proxy-companion. If for some reason these files are not generated, remember that you can find samples in the repository.
    ```sh
    $ cat .env.prod
    $ cat .env.prod.db
    $ cat .env.prod.proxy-companion
    $ cat .env.prod.grafana
    ```
2. Create docker volumes to save the data
    ```sh
    $ docker volume create --name=testjose_postgres_data
    $ docker volume create --name=static_volume
    $ docker volume create --name=media_volume
    $ docker volume create --name=certs
    $ docker volume create --name=html
    $ docker volume create --name=vhost
    $ docker volume create --name=testjose_grafana
    ```
3. Build the images and run the containers:

    ```sh
    $ docker-compose -f docker-compose.prod.yml up --build
    ```
# Production Grafana
It will automatically load the provisioning configurations and serve the dashboard in [http://resum.me:3000](http://resum.me:3000).


# Hints to work with this project

1. Execute a command inside de Django container in **DEVELOPMENT** environment.
    ```sh
    $ docker-compose exec web python manage.py shell
    ```
2. Execute a command inside de Django container in **PRODUCTION** environment.
    ```sh
    $ docker-compose -f docker-compose.prod.yml exec web python manage.py shell
    ```
3. Create Django migrations. In case you want to execute this in production environment, remember to use the -f parameter.
    ```sh
    $ docker-compose exec web python manage.py makemigrations
    ```
4. Execute Django migrations. In case you want to execute this in production environment, remember to use the -f parameter.
    ```sh
    $ docker-compose exec web python manage.py migrate
    ```
   
5. Create Django admin superuser. In case you want to execute this in production environment, remember to use the -f parameter.
    ```sh
    $ docker-compose exec web python manage.py createsuperuser
    ```
6. To configure the debugger in Pycharm you can use guide in the following [link](https://testdriven.io/blog/django-debugging-pycharm/#:~:text=To%20do%20so%2C%20open%20PyCharm,create%20a%20new%20Docker%20configuration.). Remember that you must have a professional license to use this feature.
