# Make the elections ONLINE

VOTA (VOTing Application) is a web application compatible with mobile devices to vote online. Configure parties and candidates from the console and add the rules according to the electoral law of the country. All voting information is stored in a database

It works with:

- JDK 18
- Gradle 7.4.2
- React JS 16.13.1
- Spring Boot 2.7.1
- Docker 20.10.00
- Keycloak 15.1.1
- Cities generator 1.2.0

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

unzip the downloaded zip file, go in the unzipped folder and run:
```
COUNTRY=${COUNTRY} docker-compose up
```
Where COUNTRY is the country where the voting would be start. Actually are available en or it.
You are ready now to connect to: [http://vota-frontend.vige.it](http://vota-frontend.vige.it) and: [http://vota-report.vige.it](http://vota-report.vige.it)

If you use Chrome, by default you have a CORS protection for the internal lan servers. So you need to disable the internal network protection clicking on [chrome://flags/#block-insecure-private-network-requests](chrome://flags/#block-insecure-private-network-requests) and disabling the first voice

## Build and start the single projects

### Start votingpapers

the rest services to vote

To build the application run the command inside the votingpapers folder
```
./gradlew build
```
Start the Java application with the following commands:
```
java -jar build/libs/votingpapers-1.0.0-SNAPSHOT.jar --server.port=8180 --spring.profiles.active=dev
```
and open http://localhost:8180/swagger-ui/index.html in your browser to connect to the vote application.

If you need to start it on a environment production:
```
java -Djavax.net.ssl.trustStore=./application.keystore -Djavax.net.ssl.trustStorePassword=password -jar build/libs/votingpapers-1.0.0-SNAPSHOT.jar --server.ssl.key-store=./application.keystore --server.ssl.key-store-password=password --server.ssl.trust-store=./application.keystore --server.ssl.trust-store-password=password --server.port=8543 --spring.profiles.active=prod
```

#### Docker

If you need a complete environment you can download docker and import the application through the command:
```
docker pull vige/vota-votingpapers
```
To run the image use the command:
```
docker run -d --name vota-votingpapers -p8180:8180 vige/vota-votingpapers
```
Then open [http://vota-votingpapers.vige.it:8180/swagger-ui/index.html](http://vota-votingpapers.vige.it:8180/swagger-ui/index.html) to connect to the vote application

### Start voting

To build the application run the command inside the voting folder
```
./gradlew build -x test
```
Start the Java application with the following commands:
```
java -jar build/libs/voting-1.0.0-SNAPSHOT.jar --server.port=8080 --spring.profiles.active=dev
```
and open http://localhost:8080/swagger-ui/index.html in your browser to connect to the vote application.

If you need to start it on a environment production:
```
java -Djavax.net.ssl.trustStore=./application.keystore -Djavax.net.ssl.trustStorePassword=password -jar build/libs/voting-1.0.0-SNAPSHOT.jar --server.ssl.key-store=./application.keystore --server.ssl.key-store-password=password --server.ssl.trust-store=./application.keystore --server.ssl.trust-store-password=password --server.port=8443 --spring.profiles.active=prod
```

#### Docker

If you need a complete environment you can download docker and import the application through the command:
```
docker pull vige/vota-voting
```
To run the image use the command:
```
docker run -d --name vota-voting -p8080:8080 vige/vota-voting
```
Then open [http://vota-voting.vige.it:8080/swagger-ui/index.html](http://vota-voting.vige.it:8080/swagger-ui/index.html) to connect to the vote application

### Start history

the rest services to vote

To build the application run the command inside the history folder
```
./gradlew build
```
Start the Java application with the following commands:
```
./gradlew startMongoDb
```
to start a MongoDB instance. Then:
```
java -jar build/libs/history-1.0.0-SNAPSHOT.jar --server.port=8280 --spring.profiles.active=dev
```
and open http://localhost:8280/swagger-ui/index.html in your browser to connect to the vote application.

If you need to start it on a environment production:
```
java -Djavax.net.ssl.trustStore=./application.keystore -Djavax.net.ssl.trustStorePassword=password -jar build/libs/history-1.0.0-SNAPSHOT.jar --server.ssl.key-store=./application.keystore --server.ssl.key-store-password=password --server.ssl.trust-store=./application.keystore --server.ssl.trust-store-password=password --server.port=8643 --spring.profiles.active=prod
```

#### Docker

If you need a complete environment you can download docker and import the application through the command:
```
docker pull vige/vota-history
```
To run the image use the command:
```
docker run -d --name vota-history -p8280:8280 vige/vota-history
```
Then open [http://vota-history.vige.it:8280/swagger-ui/index.html](http://vota-history.vige.it:8280/swagger-ui/index.html) to connect to the vote application

### Start frontend

Go in the frontend folder and run npm through the following commands:
```
npm install
npm start
```

And connect to [http://localhost:3000](http://localhost:3000)

You would update the online application in [http://vota-frontend.vige.it](http://vota-frontend.vige.it) url. Simply digit in your frontend folder the command:
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
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout server.key -out server.crt -subj "/C=GB/ST=London/L=London/O=Global Security/OU=IT Department/CN=localhost"
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
Now you can connect in the application going to: open [https://localhost](https://localhost)

#### Online application

You can see the updated online application simply connecting to:
Then open [http://vota-frontend.vige.it](http://vota-frontend.vige.it)

### Start report

Go in the report folder and run npm through the following commands:
```
npm install
npm start
```

And connect to [http://localhost:3000](http://localhost:3000)

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
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout server.key -out server.crt -subj "/C=GB/ST=London/L=London/O=Global Security/OU=IT Department/CN=localhost"
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
Now you can connect in the application going to: open https://localhost

#### Online application

You can see the updated online application simply connecting to:
Then open [http://vota-report.vige.it](http://vota-report.vige.it)

else you can choose the date of the elections so:
http://vota-report.vige.it?date=${yyyy-MM-dd}.
For example:
29 December 2019 - [http://vota-report.vige.it?date=2019-12-29](http://vota-report.vige.it?date=2019-12-29)

### Start auth

In development mode:

    mvn install -Pdevelopment
    mvn package -Pdevelopment,prepare-keycloak
    
In production mode:

    mvn install -Pproduction
    mvn package -Pproduction,prepare-keycloak
    
and to start the prepared keycloak instance:

    mvn install -Pdevelopment,runtime-keycloak
    
Or for the production:

    mvn install -Pproduction,runtime-keycloak -Dcitiesgenerator.url=https://cities-generator-service.vige.it:8743 -Dvotingpapers.url=https://vota-votingpapers.vige.it:8543 -Dvoting.url=https://vota-voting.vige.it:8443 -Dhistory.url=https://vota-history.vige.it:8643 -Dfrontend.url=https://localhost -Dreport.url=https://localhost:444
    
Where citiesgenerator.url, votingpapers.url, voting.url, history.url, frontend.url and report.url are the host names of the app servers to connect. If you start with the developer profile you must not specify the host names because the default host name localhost is used. If you don't declare the url variables in the mode production, the default will be localhost.
To create new users in WildFly:

$JBOSS_HOME/bin/add_user.sh

    What type of user do you wish to add? 
     a) Management User (mgmt-users.properties) 
     b) Application User (application-users.properties)
    (a): b

Enter the details of the new user to add.
Realm (ApplicationRealm) : 
Username : user2
Password : password2
Re-enter Password : password2
What roles do you want this user to belong to? (Please enter a comma separated list, or leave blank for none) : users
The username 'admin' is easy to guess
Are you sure you want to add user 'admin' yes/no? yes

to test the rest api with junit:

    deploy the rest api in a server
    mvn -Prest-keycloak-test test

To debug the application using Eclipse you can put this parameter:

    mvn -Dmaven.surefire.debug test

It will start on the 5005 port.

The tests are done using Chrome 96.0.4664.93 (64-bit) on WildFly 24.0.1.Final

#### Docker

To install the docker image run the command:

    docker pull vige/vota-auth
    
To run the image run the command:

    docker run -p 8480:8480 --name vota-auth vige/vota-auth
    
If you want start it in background mode:

    docker run -p 8480:8480 -d --name vota-auth vige/vota-auth

Both the executions will run using localhost as host connection name. If you need to specify a different host, for example if you are in a remote cloud, you must specify the hosts for keycloak and the vota app so:

    docker run -p 8480:8480 -e CITIESGENERATOR_URL=${citiesgenerator.url} -e VOTINGPAPERS_URL=${votingpapers.url} -e VOTING_URL=${voting.url} -e HISTORY_URL=${history.url} -e FRONTEND_URL=${frontend.url} -e REPORT_URL=${report.url} -e KEYCLOAK_URL=${keycloak.url} -d --name vota-auth vige/vota-auth
    
If you need a different language by the english you can set the i18 variable. A sample to start the docker container with a italian language:

    docker run -p 8480:8480 -e LC_ALL=it_IT.UTF-8 -d --name vota-auth vige/vota-auth

If you want to configure, add votes, classes and new users or approve users connect to: [http://localhost:8480/auth/admin/vota-domain/console](http://localhost:8480/auth/admin/vota-domain/console) with root/gtn in the keycloak webapp.
If you want connect in the keycloak webapp as superuser connect to it with admin/admin

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
