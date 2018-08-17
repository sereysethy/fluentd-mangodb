# Docker

Copy file `docker-compose.example.yml` to `docker-compose.yml`

```
cp docker-compose.example.yml docker-compose.yml
```

# Fluentd

Create configuration in folder `conf/fluentd` by renaming them using `*.example.cfg`

Verify the main configuration file `conf/fluentd/fluentd.cfg`. This file includes other configurations file.

Enable this in `docker-compose.yml` by removing following comments:

```
environment:
  - FLUENTD_CONF=fluentd.cfg
```

Configure appropriate username/password for each `match` section in the configuration files.

# MongoDB

## Administration user

Create a password for user `root`. If it is a production server, make sure to create a strong password.

## How to create username application/docker user

Log in to mongo container

```
$ mongo -u root -p example --authenticationDatabase admin fluentd
> db.createUser({ user : "docker", pwd : "example", roles: [ {db: "fluentd", role: "readWrite"}]})
Successfully added user: {
	"user" : "docker",
	"roles" : [
		{
			"db" : "fluentd",
			"role" : "readWrite"
		}
	]
}
> exit
$ mongo -u docker -p example --authenticationDatabase fluentd
MongoDB shell version v4.0.1
connecting to: mongodb://127.0.0.1:27017
MongoDB server version: 4.0.1
> 
```
Or

```
mongo admin -u root -p example
```

## How to test it: 

We can use a simple `nginx` docker image
```
docker run --log-driver=fluentd --log-opt tag="mongo.{{.ImageName}}/{{.Name}}/.ID}}" -p 8080:80 nginx
```

Point a browser to port `8080`

### Mongo-Express

Only use it for private development purpose ONLY. Never enable it on production server.