# Make the elections ONLINE

VOTA (VOTing Application) is a web application compatible with mobile devices to vote online. Configure parties and candidates from the console and add the rules according to the electoral law of the country. All voting information is stored in a database

It works with:

- JDK 20
- Gradle 8.1
- React JS 18.2.0
- Spring Boot 2.7.5
- Docker 20.10.17
- Keycloak 21.1.1
- Cities generator 1.2.2

## Start the application

Download the Installer from `{{ site.github.zip_url }}`

Install Docker from [https://www.docker.com/get-started](https://www.docker.com/get-started)

Configure in your /etc/hosts file the dns names:
```
$IP_ADDRESS  cities-generator-service.vige.it
$IP_ADDRESS  vota-auth.vige.it
$IP_ADDRESS  vota-votingpapers.vige.it
$IP_ADDRESS  vota-voting.vige.it
$IP_ADDRESS  vota-history.vige.it
```
where in $IP_ADDRESS you must choose the ip addresses where are located the servers

unzip the downloaded zip file, go in the unzipped folder, choose the dev or prod folder according your preferred mode and run:
```
COUNTRY=${COUNTRY} docker-compose up
```
Where COUNTRY is the country where the voting would be start. Actually are available en or it.
You are ready now to connect to: [https://vota-frontend.vige.it](https://vota-frontend.vige.it) and: [https://vota-report.vige.it](https://vota-report.vige.it)

If you use Chrome in a dev mode, by default you have a CORS protection for the internal lan servers. So you need to disable the internal network protection clicking on [chrome://flags/#block-insecure-private-network-requests](chrome://flags/#block-insecure-private-network-requests) and disabling the first voice

## Build and start the single projects

### Start votingpapers

the rest services to vote

To build the application run the command inside the votingpapers folder
```
./gradlew build
```
Start the Java application with the following commands:
```
java -jar build/libs/votingpapers-1.1.1.jar --server.port=8180 --spring.profiles.active=dev
```
and open http://vota-votingpapers.vige.it:8180/swagger-ui/index.html in your browser to connect to the vote application.

If you need to start it on a environment production:
```
java -Djavax.net.ssl.trustStore=./docker/prod/volume/cert/application.keystore -Djavax.net.ssl.trustStorePassword=password -jar build/libs/votingpapers-1.1.1.jar --server.ssl.key-store=./docker/prod/volume/cert/application.keystore --server.ssl.key-store-password=password --server.ssl.trust-store=./docker/prod/volume/cert/application.keystore --server.ssl.trust-store-password=password --server.port=8543 --spring.profiles.active=prod
```

#### Docker

If you need a complete environment you can download docker and import the application through the command:
```
docker pull vige/vota-votingpapers
```
To run the image use the command:
```
docker run -d --name vota-votingpapers -p8543:8543 vige/vota-votingpapers
```
Then open [https://vota-votingpapers.vige.it:8543/swagger-ui/index.html](https://vota-votingpapers.vige.it:8543/swagger-ui/index.html) to connect to the vote application

### Start voting

To build the application run the command inside the voting folder
```
./gradlew build -x test
```
Start the Java application with the following commands:
```
java -jar build/libs/voting-1.1.1.jar --server.port=8080 --spring.profiles.active=dev
```
and open http://vota-voting.vige.it:8080/swagger-ui/index.html in your browser to connect to the vote application.

If you need to start it on a environment production:
```
java -Djavax.net.ssl.trustStore=./docker/prod/volume/cert/application.keystore -Djavax.net.ssl.trustStorePassword=password -jar build/libs/voting-1.1.1.jar --server.ssl.key-store=./docker/prod/volume/cert/application.keystore --server.ssl.key-store-password=password --server.ssl.trust-store=./docker/prod/volume/cert/application.keystore --server.ssl.trust-store-password=password --server.port=8443 --spring.profiles.active=prod
```

#### Docker

If you need a complete environment you can download docker and import the application through the command:
```
docker pull vige/vota-voting
```
To run the image use the command:
```
docker run -d --name vota-voting -p8443:8443 vige/vota-voting
```
Then open [https://vota-voting.vige.it:8443/swagger-ui/index.html](https://vota-voting.vige.it:8443/swagger-ui/index.html) to connect to the vote application

### Start history

the rest services to vote

To build the application run the command inside the history folder
```
./gradlew build
```
Start the Java application with the following commands:
```
docker run -p27017:27017 arm64v8/mongo:6.0
```
to start a MongoDB instance. Then:
```
java -jar build/libs/history-1.1.1.jar --server.port=8280 --spring.profiles.active=dev
```
and open http://vota-history.vige.it:8280/swagger-ui/index.html in your browser to connect to the vote application.

If you need to start it on a environment production:
```
java -Djavax.net.ssl.trustStore=./docker/prod/volume/cert/application.keystore -Djavax.net.ssl.trustStorePassword=password -jar build/libs/history-1.1.1.jar --server.ssl.key-store=./docker/prod/volume/cert/application.keystore --server.ssl.key-store-password=password --server.ssl.trust-store=./docker/prod/volume/cert/application.keystore --server.ssl.trust-store-password=password --server.port=8643 --spring.profiles.active=prod
```

#### Docker

If you need a complete environment you can download docker and import the application through the command:
```
docker pull vige/vota-history
```
To run the image use the command:
```
docker run -d --name vota-history -p8643:8643 vige/vota-history
```
Then open [https://vota-history.vige.it:8643/swagger-ui/index.html](https://vota-history.vige.it:8643/swagger-ui/index.html) to connect to the vote application

### Start frontend

Go in the frontend folder and run npm through the following commands:
```
npm install
npm start
```

And connect to [http://vota-frontend.vige.it:3000](http://localhost:3000)

You would update the online application in [https://vota-frontend.vige.it](https://vota-frontend.vige.it) url. Simply digit in your frontend folder the command:
```
npm run deploy
```

If you need a production environment go in the frontend folder and run npm through the following commands:
```
npm install
npm ci --only=production
npm run build
```
Then create a SSL certificate for the https. Here a sample:
```
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout server.key -out server.crt -subj "/C=GB/ST=London/L=London/O=Global Security/OU=IT Department/CN=vota-frontend.vige.it"
```
and copy it in the home directory under the .https-serve folder.

Now to start the application install the https server:
```
sudo npm install -g https-serve
```
Then go in the build folder and start with the command:
```
https-serve -s build
```
Now you can connect in the application going to: open [https://vota-frontend.vige.it](https://localhost)

#### Online application

You can see the updated online application simply connecting to:
Then open [https://vota-frontend.vige.it](https://vota-frontend.vige.it)

### Start report

Go in the report folder and run npm through the following commands:
```
npm install
npm start
```

And connect to [http://vota-report.vige.it:3000](http://vota-report.vige.it:3000)

You would update the online application in [http://vota-report.vige.it](http://vota-report.vige.it) url. Simply digit in your report folder the command:
```
npm run deploy
```

If you need a production environment go in the frontend folder and run npm through the following commands:
Go in the report folder and run npm through the following commands:
```
npm install
npm ci --only=production
npm run build
```
Then create a SSL certificate for the https. Here a sample:
```
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout server.key -out server.crt -subj "/C=GB/ST=London/L=London/O=Global Security/OU=IT Department/CN=vota-report.vige.it"
```
and copy it in the home directory under the .http-serve folder.

Now to start the application install the https server:
```
sudo npm install -g https-serve
```
Then go in the build folder and start with the command:
```
https-serve -s build
```
Now you can connect in the application going to: open https://vota-report.vige.it

#### Online application

You can see the updated online application simply connecting to:
Then open [http://vota-report.vige.it](http://vota-report.vige.it)

else you can choose the date of the elections so:
http://vota-report.vige.it?date=${yyyy-MM-dd}.
For example:
29 December 2019 - [http://vota-report.vige.it?date=2019-12-29](http://vota-report.vige.it?date=2019-12-29)

### Start auth

In development mode:

    mvn install -Pdevelopment -Dcitiesgenerator.url=http://cities-generator-service.vige.it:8380 -Dvotingpapers.url=http://vota-votingpapers.vige.it:8180 -Dvoting.url=http://vota-voting.vige.it:8080 -Dhistory.url=http://vota-history.vige.it:8280 -Dfrontend.url=http://vota-frontend.vige.it:3000
    
Where citiesgenerator.url, votingpapers.url, voting.url, history.url, frontend.url and report.url are the host names of the app servers to connect.
    
In production mode:

    mvn install -Pproduction -Dcitiesgenerator.url=https://cities-generator-service.vige.it:8743 -Dvotingpapers.url=https://vota-votingpapers.vige.it:8543 -Dvoting.url=https://vota-voting.vige.it:8443 -Dhistory.url=https://vota-history.vige.it:8643 -Dfrontend.url=https://vota-frontend.vige.it

#### Docker develop image
------------

To install the docker image run the command:
```
    docker pull vige/vota-auth
```
To run the image run the command:
```
    docker run -p 8480:8480 --name vota-auth vige/vota-auth
```
If you want start it in background mode:
```
    docker run -p 8480:8480 -d --name vota-auth vige/vota-auth
```
If you want to configure, add votes, classes and new users or approve users connect to: [http://vota-auth.vige.it:8480/admin/vota-domain/console](http://vota-auth.vige.it:8480/admin/vota-domain/console) with root/gtn in the keycloak webapp.
If you want connect in the keycloak webapp as superuser connect to it with root/gtn

#### Docker production image
------------

To install the docker image run the command:
```
    docker pull vige/vota-auth
```
To run the image run the command:
```
    docker run -p 8843:8843 --name vota-auth vige/vota-auth
```
If you want start it in background mode:
```
    docker run -p 8843:8843 -d --name vota-auth vige/vota-auth
```
```
If you want to configure, add votes, classes and new users or approve users connect to: [https://vota-auth.vige.it:8843/admin/vota-domain/console](ttps://vota-auth.vige.it:8843/admin/vota-domain/console) with root/gtn in the keycloak webapp.
If you want connect in the keycloak webapp as superuser connect to it with root/gtn

### DNS configuration

If you work in a production environment you need to configure the dns.
Add the following DNS in your /etc/hosts file:
```
$IP_ADDRESS  cities-generator-service.vige.it
$IP_ADDRESS  vota-auth.vige.it
$IP_ADDRESS  vota-votingpapers.vige.it
$IP_ADDRESS  vota-voting.vige.it
$IP_ADDRESS  vota-history.vige.it
```

where in $IP_ADDRESS you must choose the ip addresses where are located the servers
