Apache DS in a Docker Container
===============================

Apache DS is a Java implementation of Directory server (**LDAP**). This projects puts it into container and makes it easier to configure and bootstrap it with some data.

[Apache DS Page](1) that explains what it is.

[Docker registry entry](3) for this container.

## Running container
Simply run the container with docker:

	
	docker run -d -p 10389:10389 greggigon/apacheds
	



This starts a default instance of Apache DS running on port 10389 with no SSL. It has a default admin user and password and all default Apache DS schemas. 

> user: **uid=admin,ou=system** password: **secret**


## Configuration 
Container has two volumes defined:

* **/data** - if you want to persiste container data somewhere
* **/bootstrap** - for configuration, schema and bootstraping file

### Bootstraping

If you want to bootstrap Apache DS with a specific **schema** mount a bootstrap volume and place Apache DS specific schema in it.


	docker run -d -p 10389:10389 -v /onmyhost:/bootstrap greggigon/apacheds

Sample schema can be found in a [sample/schema.tar](2) in this GitHub repository. 

If you want to use specific **config.ldif** to setup Apache DS place it in the mounted volume for **/bootstrap** directory. Container will pick it up automaticaly.

If you have some extra data you want to put into Directory, you can place a file in the **/bootstrap** mounting folder and setup Environment variable **BOOTSTRAP_FILE**.

	docker run -d -p 10389:10389 -v /onmyhost:/bootstrap -e BOOTSTRAP_FILE=/bootstrap/the-file.ldif greggigon/apacheds


### Examples

[sample](2) folder containes example schema, example config file and example of a data creation in LDAP upon bootstrap.

> Pull request for contributions are always WELCOME! :)


## Composer with custom config for organization "actinolix"
### Create volume
	docker volume create --name apacheds_data_vol --driver local
	docker volume create --name apacheds_bootstrap_vol --driver local
	docker inspect apacheds_bootstrap_vol

### copy customized bootstrap file

####copy the user specific file  ./samples/bootstrap-data-in-apacheds.ldif to 
	docker inspect apacheds_bootstrap_vol  | grep Mountpoint | awk  '{print $2}'

### create partition manually as per:
https://directory.apache.org/apacheds/basic-ug/1.4.3-adding-partition.html

###
	cp ./sample/bootstrap-data-in-apacheds.ldif "/var/lib/docker/volumes/apacheds_bootstrap_vol/_data"
	cp ./sample/config.ldif "/var/lib/docker/volumes/apacheds_bootstrap_vol/_data"

### Re-Build image
	docker-compose -f docker-compose.yaml up --force-recreate

## Start
	docker-compose up -d

[1]: https://directory.apache.org/apacheds/
[2]: https://github.com/greggigon/apacheds-docker-container/tree/master/sample
[3]: https://registry.hub.docker.com/u/greggigon/apacheds/