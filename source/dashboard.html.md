---
title: Kapow Documentation

toc_footers:
  - <a href='./index.html'>Introduction</a>
  - <a href='./dashboard.html'>Developer Dashboard</a>
  - <a href='./client-api-reference.html'>Client API Reference</a>
  - <a href='./server-api-reference.html'>Server API Reference</a>
  - <a href='./debugging.html'>Debugging</a>
  - <a href='./faq.html'>FAQ</a>

---

# Developer Dashboard

You can sign up on the [Kapow Developer Dashboard](https://dev.kapow.games) with your official email address (ideally one that is accessible to your engineering and product leads). 

A Kapow game undergoes three stages:
### Beta
During development, developers can specify a URL where the game is hosted. This can be the IP address of any machine where a web server is running. Each time the game is loaded within the Kapow app, it will display the latest state of the code that is served by the web server.
A beta game is visible only to those users who you've whitelisted as beta-testers within the [distribution](#distribution) section.

### Review
Once you want the Kapow Team to review your game, you can submit a build for review by uploading a zip-file that contains the client-side code for the game. The game then becomes visible to the entire Kapow team. To submit update review builds, you can make the changes in your `Beta` game and submit another build for Review.

### Released
Once the Kapow Team has cleared the build for release, they'll push the review build to a `release` state, post which every Kapow user will be able to access your game.
As a result of this, the review game will disappear from your list of games on the dashboard.

# Configuration
### Game URL/Game ZIP
URL to the location where the game is hosted. Eg: `https://example.com/game/index.html` or the zip file that contains the client-side code for the game. The zip file should be structure such that on un-zipping, a new folder is created that contains the entire code for the game.
<aside class="notice">
Please make sure that the server-side code is not a part of this zip file.
</aside>

### server.js
The file that contains your server side code.

### Number of Players
When a player chooses to play against random opponents, Kapow will lock the room if auto-room-locking is enabled once these many players have joined the room. The game does have the option to unlock the room post this.

### Auto Room Locking
This is to tell Kapow whether it should auto lock the room when the specified number of players have joined a room filled with random players.

## Distribution
For as long as your game is in the beta phase, it will not be surfaced to the general public. To let your team members view and test the game, you will have to list down their Facebook email addresses in the `Distribution` section.

<aside class="notice">
Please note that only email addresses of users who have registered on the Kapow app can be used entered here.
</aside>

## Boards
If your game contains scoreboards, you can configure them here. For more information, check out the [FAQ section](./faq.html).

## Server Logs
Logs emitted by your server script will be displayed here.
