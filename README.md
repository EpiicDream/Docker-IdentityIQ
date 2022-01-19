# Docker-IdentityIQ
Deploy an IdentityIQ instance using Docker. This project use docker-compose to deploy:
* An Apache Tomcat container with IdentityIQ.
* A MySQL container.
* A phpMyAdmin container.
* An OpenLDAP container.
* A phpLDAPadmin container.
* A SMTP server container.

All theses services are connected through a network bridge.

## Requirements

To use this project, you have to download and install [Docker](https://www.docker.com/get-started), and have an IdentityIQ zip archive with the version of your choice from [Compass](https://community.sailpoint.com/t5/IdentityIQ-Server-Software/ct-p/IdentityIQ).

## Setup

Before running any commands, you have to deposite your IdentityIQ zip archive in the root of the directory project. Then you have to edit the **.env** file and update the **IIQ_VERSION** variable by the version you are using *(Exemple: 8.2 for identityiq-8.2.zip)*. You can also specify a patch with **IIQ_PATCH** *(Exemple: p1 for identityiq-8.2p1.jar, nothing for no patch)*.

Then, go to the root of the directory and run `docker-compose up`. This command will build the **docker-identityiq_tomcat** image and create all the containers.

At the first launch, the iiq-tomcat container will install mariadb client to communicate with the mysql container, and run the *create_identityiq_tables-\<version\>.sql* script. Then it will run the iiq console, import init files, patch the database if a patch is provided, and start the tomcat server.

![first-launch](https://user-images.githubusercontent.com/23320254/149496381-6e65d475-3312-4f7b-acbc-33131798ecf9.png)
  
After that, the iiq-tomcat container will launch tomcat server after each start.

## Informations

The IdentityIQ server is available at [http://localhost:8080/identityiq/](http://localhost:8080/identityiq/).
* Admin user: spadmin
* Admin password: admin

The MySQL server listens on port 3306 and is accessible through the phpMyAdmin container at [http://localhost:8070](http://localhost:8070).
* Admin user: root
* Admin password: root

The OpenLDAP server listens on port 389 and is accessible through the phpLDAPadmin container at [https://localhost:6443](https://localhost:6443).
* LDAP domain: my-company.com
* Admin user: cn=admin,dc=my-company,dc=com
* Admin password: root

The MailSlurper server listens on port 25 and the emails are visible at [http://localhost:8090](http://localhost:8090).

## Usage

To stop the containers, use the command `docker-compose stop`.

To start the containers, use the command `docker-compose start`.

To remove the containers, use the command `docker-compose down`. *(Warning, this action deletes data from your containers)*.

To recreate the containers, use the command `docker-compose up`.

## Update version

If you want to change the IdentityIQ version or change the patch version, you must edit the **.env** file with the new version, remove the current containers using `docker-compose down`, remove the image **docker-identityiq_tomcat** using `docker image rm docker-identityiq_tomcat`, clean the old volumes using `docker volume prune` then rebuild the iiq-tomcat image and recreate the containers using `docker-compose up`.

**BE CAREFUL, this action will delete your containers and all the data they contain.**
