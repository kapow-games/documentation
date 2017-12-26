---
title: Kapow Documentation

search: true
---

# Developer Dashboard

You can sign up on the [Kapow Developer Dashboard](https://dev.kapow.games) with your official email address (ideally one that is accessible to your engineering and product leads). 

On the developer dashboard, you have the option of either uploading the game as a zip file, or providing the URL from where the game will be fetched each time.

During development, your developers can specify a URL where they have hosted your game. This can be set to the IP address of the developer's machine, and your developers can start a local web server to serve the code that they are currently working on. Then, each time they start the game inside the mobile app, it will display the latest code that is served by the web server on the developer's machine.

Games that are leveraging Kapow's [server side APIs](https://github.com/kapow-games/docs/wiki/Server-API-Reference) will also need to provide a publicly accessible link to their `server.js` file that contains code that can be invoked remotely. Kapow does not poll this file for updates, so you will have to manually click on 'Save Changes' each time there's an update you'd like to reflect on the server side.

## Configuration
### Game URL
URL to the location where the game is hosted. Eg: `https://example.com/game/index.html`.

### Server URL
URL to the location of your server side JavaScript file. Eg: `https://example.com/game/server/server.js`.

### Number of Players
When a player chooses to play against random opponents, Kapow will lock the room if [auto-room-locking](https://github.com/kapow-games/docs/wiki/Developer-Dashboard#auto-room-locking) is enabled once these many players have joined the room. The game does have the option to [unlock](https://github.com/kapow-games/docs/wiki/Server-API-Reference#unlockroomroomid-successcb-failurecb) the room post this.

### Auto Room Locking
This is to tell Kapow whether it should auto lock the room when the specified [number of players](https://github.com/kapow-games/docs/wiki/Developer-Dashboard#number-of-players) have joined a room filled with random players.

## Distribution
For as long as your game is in the beta phase, it will not be surfaced to the general public. To let your team members view and test the game, you will have to list down their Facebook email addresses in the `Distribution` section.

*Please note that only email addresses of users who have registered on the Kapow app can be used entered here.*
