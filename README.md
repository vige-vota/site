# VOTA

is a web application compatible with mobile devices to vote online. Configure parties and candidates from the console and add the rules according to the electoral law of the country. All voting information is stored in a database

It works with:

- JDK 12
- Gradle 5.5.1
- React JS 16.8.6

## Start votingpapers

the rest services to vote

To build the application run the command inside the votingpaper folder
```
./gradlew build
```
Start the Java application with the following commands:
```
./gradlew startMongoDb
```
to start a MongoDB instance. Then:
```
java -jar build/libs/votingpaper-1.0.0-SNAPSHOT.jar --server.port=8180
```
and open `http://localhost:8180/swagger-ui.html` in your browser to connect to the vote application.

If you need to start it on a environment production:
```
java -jar build/libs/votingpaper-1.0.0-SNAPSHOT.jar --server.port=8543 --server.ssl.key-store=/${your_path}/keystore.p12 --server.ssl.key-store-password=secret --server.ssl.keyStoreType=PKCS12 --server.ssl.keyAlias=tomcat
```
Before to start the HTTPS you need to create a keystore. You can use the following sample:
```
keytool -genkey -alias tomcat -storetype PKCS12 -keyalg RSA -keysize 2048 -keystore /${your_path}/keystore.p12 -validity 3650 -dname "CN=localhost, OU=Vige, O=Vige, L=Rome, S=Italy, C=IT" -storepass secret -keypass secret
```
moving the ${your_path} variable to your preferred path where put the keystore and open `https://localhost:8543/swagger-ui.html` in your browser to connect to the vote application.

### Docker

If you need a complete environment you can download docker and import the application through the command:
```
docker pull vige/vota-votingpaper
```
To run the image use the command:
```
docker run -d --name vota-votingpaper -p8543:8543 vige/vota-votingpaper
```
Then open `https://localhost:8543/swagger-ui.html` to connect to the vote application

## Start backend

To build the application run the command inside the backend folder
```
./gradlew build -x test
```
Start the Java application with the following commands:
```
./gradlew startMongoDb
```
to start a MongoDB instance. Then:
```
java -jar build/libs/backend-1.0.0-SNAPSHOT.jar --server.port=8080
```
and open `http://localhost:8080/swagger-ui.html` in your browser to connect to the vote application.

If you need to start it on a environment production:
```
java -jar build/libs/backend-1.0.0-SNAPSHOT.jar --server.port=8443 --server.ssl.key-store=/${your_path}/keystore.p12 --server.ssl.key-store-password=secret --server.ssl.keyStoreType=PKCS12 --server.ssl.keyAlias=tomcat
```
Before to start the HTTPS you need to create a keystore. You can use the following sample:
```
keytool -genkey -alias tomcat -storetype PKCS12 -keyalg RSA -keysize 2048 -keystore /${your_path}/keystore.p12 -validity 3650 -dname "CN=localhost, OU=Vige, O=Vige, L=Rome, S=Italy, C=IT" -storepass secret -keypass secret
```
moving the ${your_path} variable to your preferred path where put the keystore and open `https://localhost:8443/swagger-ui.html` in your browser to connect to the vote application.

### Docker

If you need a complete environment you can download docker and import the application through the command:
```
docker pull vige/vota-backend
```
To run the image use the command:
```
docker run -d --name vota-backend -p8443:8443 -e VOTINGPAPER_SCHEME=https -e VOTINGPAPER_HOST=localhost -e VOTINGPAPER_PORT=8543 vige/vota-backend
```
Then open `https://localhost:8443/swagger-ui.html` to connect to the vote application

## Start frontend

Go in the frontend folder and run npm through the following commands:
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
Now you can connect in the application going to: open `https://localhost`

### Docker

If you need a complete environment you can download docker and import the application through the command:
```
docker pull vige/vota-frontend
```
To run the image use the command:
```
docker run -d --name vota-frontend -p443:443 -e VOTINGPAPER_URL=https://localhost:8543/votingPapers -e BACKEND_URL=https://localhost:8443/vote vige/vota-frontend
```
Then open `https://localhost` to connect to the vote application.

If you need only to start a demo execute:
```
docker pull vige/vota-frontend:demo
```
and then:
```
docker run -d --name vota-frontend -p80:5000 vige/vota-frontend:demo
```
Then open `http://localhost` to connect to the vote application