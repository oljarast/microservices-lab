# Microservices: An exploration with GameOn

Instructions for Workshop 8 at ACM WomENcourage conference, Rome, September 2019.

Aim of this workshop is to introduce main concepts of microservices and provide rapid hands on experience.

### Presenters

 - Kate Stanley
 - Tina Selenge
 - Olja Rastic-Dulborough
 - Bethany Simpson

## Contents:
1. [Introduction to GameOn](#introduction-to-gameon)
2. [Practical 1](#practical-1)
    1. [Creating a room](#creating-a-room)
    1. [Pushing your room to IBM Cloud](#pushing-your-room-to-ibm-cloud)
    1. [Registering your room with GameOn](#registering-your-room-with-gameon)
3. [Practical 2](#practical-2)
    1. [Creating a Weather Service](#creating-a-weather-service)
    1. [Integrating your Weather service with your room](#integrating-your-weather-service-with-your-room)


#### Source material for these instructions:

 - [Slides for workshop](https://ibm.box.com/s/xkz3e0x756yoezqfajzq8ofuku92rzmm)

 - [GameOn book](https://book.gameontext.org/)

 - [GameOn site](https://gameontext.org/#/login)

 - [Starting repo for this workshop](https://github.com/oljarast/sample-room-java) (is a fork of the [GameOn sample-room-java repo](https://github.com/gameontext/sample-room-java))

## Introduction to GameOn

[GameOn!](https://gameontext.org/#/) is a throwback text-based adventure built to help you explore microservice architectures and related concepts.

It can be difficult to get a full picture of what a microservices application should look like, or to understand why it is said that microservice architectures both remove and introduce complexity at the same time.

GameOn helps you to get hands-on with microservices and start to explore how they work for yourself.

GameOn was created and is maintained by [these IBMers](https://github.com/orgs/gameontext/people).

## Practical 1

### Creating a Room

We will create a room by re-using the sample-room-java.

#### Pre-reqs

You will need:

- Apache Maven - A tool for building and managing Java based projects
- JDK - Java development kit


1. Install [Git](https://git-scm.com/downloads)

2. Install [Apache Maven](https://maven.apache.org/install.html)

    For Mac users use the following command:
    ```bash
    brew install mvn
    ```

3. Install a [JDK](https://www.oracle.com/java/technologies/jdk8-downloads.html). Check `java -version` to make sure you are using version 8.

#### Cloning the project

1. Go to this repository in GitHub https://github.com/oljarast/sample-room-java
    - Using ssh - [instructions for setting up ssh](https://help.github.com/en/articles/connecting-to-github-with-ssh)
    ```bash
    git clone git@github.com:oljarast/sample-room-java.git
    ```
    - Using https (no additonal setup required)
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

3. Stand up the server locally to see your room in localhost:
    ```bash
    mvn liberty:run-server
    ```
    Wait till you see:

    ```bash
    [INFO] [AUDIT   ] CWWKF0011I: The server gojava-room is ready to run a smarter planet.
    ```

4. Test that the room is healthy by going to http://localhost:9080/health.
    You should see that your room is available.

5. Go to: http://localhost:9080
    You should see a simple interface for your room.

6. Next to the field `TheGeneratedIdOfThisRoom` click the `Connect` button.
    You should see the json file returned in the activity box:
    ```
    Connection established.
    ```
    This means your room is working.

7. Click `roomHello`. You can see the reply in JSON format with the room information.

8. Stop the sever. In the terminal where you ran `mvn liberty:run-server`, do `ctrl+c` to stop the process

    Once the server has stopped it will print out the following:
    ```bash
    [INFO] [AUDIT   ] CWWKE0036I: The server gojava-room stopped after 14.65 seconds.
    ```

#### Updating your room

Edit the message that is returned when you run the `roomHello` command.

In your favourite editor, open the local directory for the `sample-room-java` that you cloned.

Open the `src/main/java/org/gameontext/sample/RoomDescription.java` file. Lines 40-42 have the code that contain the `name`, `fullName` and `description` strings that you just saw in your room page when you clicked roomHello.

Make some changes to the strings here, maybe rename the room to include your name and add a different description. Save the changes and go back to your terminal, run:

```bash
mvn install
```

This will rebuild your room with the changes you made in the RoomDescription file.

Now restart the server by running:

```bash
mvn liberty:run-server
```

Go to your room page, http://localhost:9080, click `connect` and then click `roomHello`.

You should see a different response returned. It should include the changes you just made.

### Pushing your room to IBM Cloud

Lets deploy the room into a cloud environment so we can have a publicly accessible endpoint that GameOn can talk to.

#### Pre-reqs

 - Sign up for IBM Cloud.
 
    Sign up for a free, trial account here https://cloud.ibm.com/registration?cm_mmc=womencourage
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
    cf push <app-name> -p target/gameon-room-java.zip -m 256M
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

## Practical 2

Now that you have built a basic room we can start extending. Many microservice applications make requests to other services. We also want to add a fun element to your room.

We will update the room to connect to a Weather Service in IBM Cloud, so that when someone types `/temp` in your room it will return a temperature.

### Creating a Weather Service

1. Go to IBM Cloud on your browser and click on `Catalog` from the top of the page.
2. Select `Internet of Things` from the catagory list and click on `Weather Company Data`. 
3. Select the region `London` 
4. Scroll down and choose the space you created earlier in this [step](#Create-a-space-in-IBM-Cloud-UK).
5. Click on `Create`.
6. Select `Service credentials` from the left menu and create a new credential. Click on `New credential` button and then `Add` on the pop up window. 
7. Click `View credentials` on the `Service credentials` you just created. 
8. Take a note of `username`, `password` and `host` for later on.

### Integrating your Weather service with your room

To add a new command we need to update the `RoomImplementation` class.

Edit `src/main/java/org/gameontext/sample/RoomImplementation.java` to add the following imports to the class:

```java
import java.io.StringReader;
import javax.json.Json;
import javax.json.JsonReader;
import javax.ws.rs.client.Client;
import javax.ws.rs.client.ClientBuilder;
import javax.ws.rs.core.MediaType;
import javax.ws.rs.core.Response;
```

Edit `src/main/java/org/gameontext/sample/RoomImplementation.java` to add another element to the switch statement in the `processCommand` function. Add it just after `case "/ping"` but before the `default` one.
```java
case "/temp":
    endpoint.sendMessage(session,
            Message.createBroadcastEvent("Temperature sent to " + username, userId, getTemperature()));

    break;
```

Now we can add some code to call the Weather Service.

Add the following function to `src/main/java/org/gameontext/sample/RoomImplementation.java`. Update the `<username>`, `<password>` and `<host>` in the new function to be those of your Weather Service credentials.

```java
public String getTemperature() {

    String username = "<username>";
    String password = "<password>";
    String host = "<host>";
    
    String authorizationHeaderName = "Authorization";
    String authorizationHeaderValue = "Basic " +
        javax.xml.bind.DatatypeConverter.printBase64Binary((username + ":" + password).getBytes());
    
    String location = "30339%3A4%3AUS";
    String weatherAPIUrl = "https://" + host + "/api/weather/v1/location/" + location + "/observations.json?language=en-US";

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
    return Integer.toString(temp);
}
```

Finally we will update the room so that it will tell people about the new command.

Add the following into `src/main/java/org/gameontext/sample/RoomImplementation.java` in the `postConstruct` method on the line after `roomDescription.addCommand("/ping", "Does this work?");`.

```java
roomDescription.addCommand("/temp", "Get temperature");
```

#### Pushing a new version of the room

Rebuild your room and push up the new version:

```bash
mvn install
cf push <app-name> -p target/gameon-room-java.zip -m 256M
```

Go to GameOn and see if you can use the new `/temp` command.

## Extension

The Weather Company service gives back lots of information, see if you can add another command that provides a different type of weather information. For example:

```java
String pressure_desc = obervation.getString("pressure_desc");
int pressure_tend = observation.getInt("pressure_tend");
int max_temp = observation.getInt("max_temp");
int min_temp = observation.getInt("min_temp");
int feels_like = observation.getInt("feels_like");
int pressure = observation.getInt("pressure");
```

You can see full details of the return object in the [rest documentation](https://twcservice.mybluemix.net/rest-api/#/).

Alternatively you can make the GameOn players ask for the weather in a different way. For example, maybe you could get them to pick up a thermometer before they can get the temperature. The GameOn book gives advice for [adding objects](https://book.gameontext.org/walkthroughs/addItemsToYourRoom.html).

If you want to change the location that the data is for you can do the following:
1. Find the latitude and longitude of the location you want

    If using Google maps you can enter a location, right-click on the pin and select `What's here?` and it will show latitude and longitude.
2. Create a String called `latLong`:
    ```java
    String latLong = "<latitude>/<longitude>";
    ```
3. Update the `String weatherAPIUrl` to equal `"https://" + host + "/api/weather/v1/geocode/" + latLong + "/observations.json?language=en-US"
