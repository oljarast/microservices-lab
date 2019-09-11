# Microservices: An exploration with GameOn

Instructions for Workshop 8 at ACM WomENcourage conference, Rome, September 2019

Presenters

Kate Stanley
Tina Selenge
Olja Rastic-Dulborough
Bethany Simpson

Aim of this workshop is to introduce main concepts of microservices and provide  rapid hands on experience.

Contents:
1. Intro to microservices
2. Why GameOn was created and what it is
3. a. Practical intro - create a room
3. b. Practical intro - push room to IBM Cloud
4. What's next
5. a. Practical intro - create a Weather service
5. b. Practical intro - integrate Weather service locally, update in IBM Cloud

GameOn was created and is maintained by [these IBMers](https://github.com/orgs/gameontext/people).

Source material for these instructions:

[GameOn book](https://book.gameontext.org/)

[GameOn site](https://gameontext.org/#/login)

[Starting repo for this workshop](https://github.com/oljarast/sample-room-java) (is a fork of the [GameON sample-room-java repo](https://github.com/gameontext/sample-room-java))



## 1. Intro to microservices
## 2. Why GameOn was created and what it is

    [Presentation for 1. and 2.]()

## 3.a. Practical intro - create a room

We will create a room by reusing the sample-room-java.

You will need:

- Maven - A tool for building and managing Java based projects
- JDK - Java development kit







Install git https://git-scm.com/downloads

Install maven https://maven.apache.org/install.html
  Windows
  Mac
  Linux

Install JDK https://www.oracle.com/java/technologies/jdk8-downloads.html
  Check java -version

From your home or c: directory

Go to this repository in GitHub https://github.com/oljarast/sample-room-java

Using ssh - instructions for setting up ssh

git clone git@github.com:oljarast/sample-room-java.git

Using https - instructions

git clone https://github.com/oljarast/sample-room-java.git

In your terminal change directory to sample-room-java that you just cloned:

cd sample-room-java

Run:
mvn install

When it's done you should see

[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------

If not, call for help

Stand up the server locally to see your room in localhost:

mvn liberty:run-server

Wait till you see:

[INFO] [AUDIT   ] CWWKF0011I: The server gojava-room is ready to run a smarter planet.

Open your browser and go to:

localhost:9080

You should see a simple interface for your room

Test quickly that the room is healthy:

Click the health link:

http://localhost:9080/health

You should see that your room is available. Go back to the room page.

Test your room.

next to the field TheGeneratedIdOfThisRoom click the Connect button

You should see the json file returned in the activity box:

Connection established.

Click roomHello

You can see the reply in JSON format with the room information.

Stop the sever. In the terminal where you ran 'mvn liberty:run-server', do 'ctrl c' to stop the process

>Show output

Edit the message that is returned when you run the roomHello command

In your favourite editor, open the local directory for the sample-room-java that you cloned. Go to the /src/main/java/org/gameontext/sample directory and open the RoomDescription.java file. Lines 40-42 have the code that contain the name, fullName and description strings that you just saw in your room page when you clicked roomHello.

Make some changes to the strings here, maybe rename the room to include your name and add a different description. Save the changes and go back to your terminal, run:

maven install

This will rebuild your room with the changes you made in the RoomDescription file.

Now run:

mvn liberty:run-server

to restart the server.

Go to your room page, click connect and then click roomHello.

You should see a different response returned. It should include the changes you just made.

Lets move on and deploy the room into a cloud environment so we can have a publicly accessible end point that other services like the map can see online

## 3.b. Practical intro - push your room into IBM Cloud

Sign up for IBM Cloud

Sign up for a free, trial account here https://cloud.ibm.com/registration

Install cloud foundry

https://docs.cloudfoundry.org/cf-cli/install-go-cli.html

on mac with homebrew

brew install cloudfoundry/tap/cf-cli


Deploy your room in IBM cloud

Log into IBM Cloud from your command line:

Create an org in IBM cloud

Create a space in IBM cloud in uk geo

cf login

provide your email and password when asked

Check your API endpoint is eu-gb

cf api https://api.eu-gb.bluemix.net

Run the command that will take the files that make up your room from your local directory, sip it up and upload it to your space on IBM cloud

cf push my-test-room -p target/gameon-room-java.zip

Go to your dashboard in IBM Cloud to see your room being started

In your dashboard, under Resource summary, click Cloud Foundry Apps

Click on my-test-room

The Overview page for your test room should open and you can see that your room is running.

## 4. What's next - register your room?

Copy the


Register your room in the Game on map

Go to https://gameontext.org/#/ to register your room to the map

Enter and then authenticate with one of the other services where you already have an account: twitter, google, facebook or GitHub

Once you are authenticated, you will be taken to The First Room

You are given some basic commands:

    Commands start with '/'.
    Use /help to list all available commands. The list will change from room to room.
    Use /exits to list all available exits.
    Use /sos to return to First Room if you're stuck.
    Rooms might try to fool you, but these three commands will always work.

CLick the map icon in top right corner to add your room.

In the Room management section, register your room that is now running in IBM Cloud.

Make sure Register a new room is selected from the Select a room drop-down.

Provide a short nickname for your room - important

Provide a descriptive title for your room - important

Paste in the WebSocket endpoint

Visit your deployed application in IBM Cloud

Change the url for your service to http - not https

http://my-test-room.eu-gb.mybluemix.net/

Copy the WebSocket address

For example: ws://my-test-room.eu-gb.mybluemix.net/room

Paste in the health endpoint

Do we want them to describe the doors at this point?

Click Register.

Visit the Game-on interactive map and see if you can see your room https://gameontext.org/interactivemap/

Go back to the your room interface here and see if you can teleport into other rooms that others in the workshop have created.

##5.a. Practical intro - create a Weather service

We want to add a fun element to your room.

Connect a to a weather service from your room and code in a response when a curtain is opened that will return a temperature

# Stand up a Weather service in IBM Cloud

##5.b. Practical intro - integrate Weather service locally, update in IBM Cloud
 What do we need once service is up

- Edit the code for your room locally on your laptop:
  a. Add security? to pom xml?
    <feature>jaxrs-2.1</feature>
    <feature>transportSecurity-1.0</feature>
    <feature>mpHealth-1.0</feature>

    also
    <sslDefault sslRef="restSSLSettings" />

   <sslOptions id="restSSLOptions" sslRef="restSSLSettings" />

   <ssl id="restSSLSettings"
       keyStoreRef="restKeyStore"
       sslProtocol="TLSv1.2" />

   <keyStore id="restKeyStore"
             location="resources/security/clientkey.jks"
             type="JKS"
             password="password"/>
where?

- Edit src/main/java/org/gameon/sample/RoomImplementation.java to add another element to the switch statement in the handleMessage, just after 'case room' but before the default one.
  case "/temperature":
    endpoint.sendMessage(session,
            Message.createBroadcastEvent(getTemperature()));

    break;  
- Add the following function to src/main/java/org/gameon/sample/RoomImplementation.java Update the username and password to be those of your Weather credentials and the host to match the weather credentials as well
     public String getTemperature() {

         String username = "<username>";
         String password = "<password>;
         String authorizationHeaderName = "Authorization";
         String authorizationHeaderValue = "Basic " +
             javax.xml.bind.DatatypeConverter.printBase64Binary((username + ":" + password).getBytes());    
         Client client = ClientBuilder.newClient();
         String location = "30339%3A4%3AUS";
         String weatherAPIUrl = "https://<host>/api/weather/v1/location/" + location + "/observations.json?language=en-US";
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

- Add this into src/main/java/org/gameon/sample/RoomImplementation.java in the postConstruct method similar to ping

      roomDescription.addCommand("/temperature", "Get temperature");



Connect to a translation service that could allow the user to read the replies in their own language.
