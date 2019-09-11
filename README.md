# Microservices: An exploration with GameOn

Instructions for Workshop 8 at ACM WomENcourage conference, Rome, September 2019.

Aim of this workshop is to introduce main concepts of microservices and provide rapid hands on experience.

### Presenters

 - Kate Stanley
 - Tina Selenge
 - Olja Rastic-Dulborough
 - Bethany Simpson

## Contents:
1. [Introduction to microservices](#introduction-to-microservices)
2. [Introduction to GameOn](#introduction-to-gameon)
3. [Practical 1](#practical-1)
    1. [Creating a room](#creating-a-room)
    1. [Pushing your room to IBM Cloud](#pushing-your-room-to-ibm-cloud)
    1. [Registering your room with GameOn](#registering-your-room-with-gameon)
4. [What's next?](#whats-next)
5. [Practical 2](#practical-2)
    1. [Creating a Weather Service](#creating-a-weather-service)
    1. [Integrating your Weather service with your room](#integrating-your-weather-service-with-your-room)


#### Source material for these instructions:

 - [GameOn book](https://book.gameontext.org/)

 - [GameOn site](https://gameontext.org/#/login)

 - [Starting repo for this workshop](https://github.com/oljarast/sample-room-java) (is a fork of the [GameOn sample-room-java repo](https://github.com/gameontext/sample-room-java))



## Introduction to microservices
## Introduction to GameOn

GameOn was created and is maintained by [these IBMers](https://github.com/orgs/gameontext/people).
    [Presentation for 1. and 2.]()

## Practical 1

### Creating a Room

We will create a room by re-using the sample-room-java.

#### Pre-reqs

You will need:

- Apache Maven - A tool for building and managing Java based projects
- JDK - Java development kit


1. Install [Git](https://git-scm.com/downloads)

2. Install [Apache Maven](https://maven.apache.org/install.html)
  Windows
  Mac
  Linux

3. Install [JDK](https://www.oracle.com/java/technologies/jdk8-downloads.html)
  Check java -version

<!-- Kate: what is this ommand for? -->
<!-- From your home or c: directory -->

#### Cloning the project

1. Go to this repository in GitHub https://github.com/oljarast/sample-room-java
    a. Using ssh - instructions for setting up ssh
    ```bash
    git clone git@github.com:oljarast/sample-room-java.git
    ```
    b. Using https - instructions
    ```bash
    git clone https://github.com/oljarast/sample-room-java.git
    ```

#### Building and running the room

1. In your terminal change directory to sample-room-java that you just cloned:
    ```bash
    cd sample-room-java
    ```

2. Build the app
    ```bash
    mvn install
    ```

    When it's done you should see

    ```bash
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    ```

    If not, call for help

3. Stand up the server locally to see your room in localhost:
    ```bash
    mvn liberty:run-server
    ```
    Wait till you see:

    ```bash
    [INFO] [AUDIT   ] CWWKF0011I: The server gojava-room is ready to run a smarter planet.
    ```

4. Test quickly that the room is healthy by going to http://localhost:9080/health.
    You should see that your room is available.

5. Go to: http://localhost:9080
    You should see a simple interface for your room.

6. Test your room. Next to the field `TheGeneratedIdOfThisRoom` click the `Connect` button.
    You should see the json file returned in the activity box:
    ```
    Connection established.
    ```

7. Click `roomHello`. You can see the reply in JSON format with the room information.

8. Stop the sever. In the terminal where you ran `mvn liberty:run-server`, do `ctrl+c` to stop the process

>Show output

#### Updating your room

Edit the message that is returned when you run the roomHello command

In your favourite editor, open the local directory for the `sample-room-java` that you cloned. Go to the `/src/main/java/org/gameontext/sample` directory and open the `RoomDescription.java` file. Lines 40-42 have the code that contain the name, fullName and description strings that you just saw in your room page when you clicked roomHello.

Make some changes to the strings here, maybe rename the room to include your name and add a different description. Save the changes and go back to your terminal, run:

```bash
maven install
```

This will rebuild your room with the changes you made in the RoomDescription file.

Now restart the server by running:

```bash
mvn liberty:run-server
```

Go to your room page, http://localhost:9080, click connect and then click roomHello.

You should see a different response returned. It should include the changes you just made.

Lets move on and deploy the room into a cloud environment so we can have a publicly accessible end point that other services like the map can see online.

### Pushing your room to IBM Cloud

#### Pre-reqs

 - Sign up for IBM Cloud.
    Sign up for a free, trial account here https://cloud.ibm.com/registration
 - Install [cloud foundry command line](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)
    On mac with homebrew:
    ```bash
    brew install cloudfoundry/tap/cf-cli
    ```

#### Create a space in IBM Cloud UK

1. Log into [IBM Cloud](https://cloud.ibm.com/login)
1. Click `Manage` in the top menu bar and select `Account`
1. In the left-hand side select `Account resources` and choose `Cloud Foundry Orgs`
1. Select your organisation (there should be only one)
1. Click `Add a space`
1. Select the region `United Kingdom`
1. Enter `womencourage` as the name
1. Click `Save`

#### Deploy your room in IBM Cloud

1. Log into IBM Cloud from your command line:

    ```bash
    cf login -a https://api.eu-gb.bluemix.net -s womencourage
    ```
2. Provide your email and password when asked

3. Navigate to the directory with your room in and build the app:
    ```bash
    cd sample-room-java
    mvn install
    ```
4. Push the built app to IBM Cloud: (replace app-name with a unique name, e.g. olja-rast-room)

    ```bash
    cf push <app-name> -p target/gameon-room-java.zip
    ```

The `gameon-room-java.zip` file includes all the code for your directory and the previous command pushes to your IBM Cloud into the space `womencourage`.

#### See your app running in IBM Cloud

Go to IBM Cloud in your browser and select `IBM Cloud` in the top left to return to your Dashboard.

In the `Resource summary` panel select `Cloud Foundry apps` and click on `<app-name>.mybluemix.net`.

The `Overview` page for your application should show a green circle and say "Running".

Click `Visit App URL` to view your running room.

There are two endpoints:
 - WebSocket Endpoint
 - Health Check (REST)

Note these down for later.

### Registering your room with GameOn

1. Go to https://gameontext.org/#/ to register your room
2. Click `Enter` and then authenticate with one of the services where you already have an account: Twitter, Google, Facebook or GitHub
3. Once you are authenticated, click the icon in the top right to `Edit registered rooms`
4. Provide a full name for your room
4. Provide a short nickname for your room (with no spaces)
5. Provide a description for your room
6. Scroll down to `Connection Details`
7. Enter the Health Check URL into the `Health endpoint` box
    e.g. `https://<app-name>.mybluemix.net/health`
8. Enter the WebSockect Endpoint into the `WebSockect endpoint` box
    e.g. `ws://<app-name>.eu-gb.mybluemix.net/room`
9. Click `Register`
10. Visit the Game-on interactive map and see if you can see your room https://gameontext.org/interactivemap/

#### Visit your room in GameOn

In the game use the following commands to visit your room:
1. `/sos`
2. `/listmyrooms`
3. `/teleport <roomid>` using the roomid returned from the previous command

## What's Next?

Slide session

## Practical 2

We want to add a fun element to your room.

Connect to a Weather Service in IBM Cloud from your room so that when someone types `/temp` in your room it will return a temperature.

### Creating a Weather Service

<!-- TODO -->

### Integrating your Weather service with your room

First add a new command to your room.

Edit `src/main/java/org/gameon/sample/RoomImplementation.java` to add another element to the switch statement in the `handleMessage` function. Add it just after `case room` but before the `default` one.
```java
case "/temp":
    endpoint.sendMessage(session,
            Message.createBroadcastEvent(getTemperature()));

    break;
```

Second add some code to call the Weather Service.

Add the following function to `src/main/java/org/gameon/sample/RoomImplementation.java`. Update the `<username>` and `<password>` to be those of your Weather Service credentials and the `<host>` to match the Weather Service credentials as well.

```java
public String getTemperature() {

    String username = "<username>";
    String password = "<password>";
    String authorizationHeaderName = "Authorization";
    String authorizationHeaderValue = "Basic " +
        javax.xml.bind.DatatypeConverter.printBase64Binary((username + ":" + password).getBytes());
    
    String location = "30339%3A4%3AUS";
    String weatherAPIUrl = "https://<host>/api/weather/v1/location/" + location + "/observations.json?language=en-US";

    Client client = ClientBuilder.newClient();
    Response httpResponse = client.target(weatherAPIUrl).path("").request(MediaType.APPLICATION_JSON).header(authorizationHeaderName, authorizationHeaderValue).get();
    if (httpResponse.getStatus() != 200) {
        return "Unexpected HTTP Response: " + httpResponse.getStatus();
    }
    String responseString = httpResponse.readEntity(String.class);
    JsonReader jsonReader = Json.createReader(new StringReader(responseString));
    JsonObject responseObject = jsonReader.readObject();
    JsonObject observation = responseObject.getJsonObject("observation");
    int temp = observation.getInt("temp");
    return temp.toString();
}
```

Finally we will update the room so that it will tell people about the new command.

Add the following into `src/main/java/org/gameon/sample/RoomImplementation.java` in the `postConstruct` method similar to ping.

    ```java
    roomDescription.addCommand("/ping", "Does this work?");
    roomDescription.addCommand("/temp", "Get temperature");
    ```

#### Pushing a new version of the room

Rebuild your room and push up the new version:

```bash
mvn install
cf push <app-name> -p target/gameon-room-java.zip
```

Go to GameOn and see if you can use the new `temp` command.

## Extension

Connect to a translation service that could allow the user to read the replies in their own language.
