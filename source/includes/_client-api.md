# Game Management
## Start Game
> Create a solo room (offline):

```javascript
kapow.game.start({
  type: 'solo',
  onSuccess: function(room) {
    console.log("Solo room created", room);
    // Start game play here
  },
  onFailure: function(error) {
    console.error("Something went wrong", error);
  }
});
```
> Create a room with a specified set of friends:

```javascript
kapow.game.start({
  type: 'friends',
  players: ['alice@kapow.games', 'bob@kapow.games'],
  onSuccess: function(room) {
    console.log("Solo room created", room);
    // Start game play here
  },
  onFailure: function(error) {
    console.error("Something went wrong", error);
  }
});
```
> Pop up dialog to pick friends to play with:

```javascript
kapow.game.start({
  type: 'friends',
  onSuccess: function(room) {
    console.log("Solo room created", room);
    // Start game play here
  },
  onFailure: function(error) {
    console.error("Something went wrong", error);
  }
});
```
> Create a room with random players:

```javascript
kapow.game.start({
  type: 'random',
  attributes: {
    "difficulty": "medium"
  },
  onSuccess: function(room) {
    console.log("Solo room created", room);
    // Start game play here
  },
  onFailure: function(error) {
    console.error("Something went wrong", error);
  }
});
```
Starts a game by creating a [room](#room) according to the specified parameters.

Parameter | Description
--------- | -----------
type | Type of room to be created. Possible values: `solo`/`friends`/`random`.
players | Array of player-ids with whom you want to create a room. Eg. `[alice@kapow.games, bob@kapow.games]`. If this value isn't mentioned, and the `type` is `friends`, Kapow will pop up the friend-selection dialog.
attributes | JSON object that'll be used to match players with each other in case of a `random` game.

## Add Player
```javascript
kapow.game.addPlayer({
  onSuccess: function(room) {
    console.log("Room with invited player", room);
    // Resume game play here
  },
  onFailure: function(error) {
    console.error(error);
  }
});
```
Displays the native overlay to select and add a player to the active room. Will return the updated [room](#room) in the success callback.

## Rematch
```javascript
kapow.game.rematch({
  onSuccess: function(room) {
    console.log("Rematch room created", room);
    // Start game play here
  },
  onFailure: function(error) {
    console.error(error);
  }
});
```
Creates a new [room](#room) with the same set of players as there are in the existing one, and sends out an invitation to all of them, ensuring that even if all players in the room request for a rematch simultaneously, only a single [room](#room) is created for all of them.

## End Game
<aside class="notice">
Only solo games can be ended from the client. To end a multiplayer game, the `kapow.game.end` API needs to be triggered from the server.
</aside>
```javascript
kapow.game.end({
  onSuccess: function() {
    console.log("Solo game ended");
    // Show the game-ended screen
  },
  onFailure: function(error) {
    console.error(error);
  }
});
```
Tells Kapow that the solo game is over, and the app will stop listing the game in its list of currently active rooms.

# Room Management

## Display Active Rooms
```javascript
kapow.displayActiveRooms({
  onDismiss: function() {
    console.log("Dialog dismissed")
  }
});
```
Displays a pop up that shows the list of active rooms, ones where the game hasn't ended yet, that the user is a part of.
Selecting one of these rooms, will trigger the [load](#load) event.

## Get Active Rooms
```javascript
kapow.getActiveRooms({
  onSuccess: function(rooms) {
    for (var roomIndex in rooms) {
      console.log("Room #" + roomIndex + 1 + ": ", rooms[roomIndex]);
    }
  },
  onFailure: function(error) {
    console.error(error);
  }
})
```
Returns the list of active rooms, ones where the game hasn't ended yet, that the user is a part of.

## Load Room
```javascript
kapow.loadRoom({
  roomId: 'room@kapow.games',
  onSuccess: function(room) {
    console.log("Loaded room: ", room);
    startGamePlay(room);
  },
  onFailure: function(error) {
    console.error(error);
  }
});
```
Loads the context of a [room](#room) indicated by the `roomId` to the WebView, granting access to all room specific APIs and attaching all related lifecycle callbacks.
For example: Call this when you want to take the user to the last room he was a part of (as fetched from `getActiveRooms`) on game load.

Parameter | Description
--------- | -----------
roomId | Identifier of the room to be loaded into context.

## Unload Room
```javascript
kapow.unloadRoom({
  onSuccess: function() {
    console.log("Unloaded room successfully");
    // Take the user to the game's home screen
  },
  onFailure: function(error) {
    console.error(error);
  }
});
```
Unloads the context of a [room](#room) from the WebView, removing access to all room specific APIs and detaching all related lifecycle callbacks.
For example: Call this when the user clicks on `Start a new game` while waiting to get matched with a random opponent/shares an invite link and no one has joined yet.

# History
```javascript
kapow.history.fetch({
  packetId: '1517568717299-n8e-m202',
  roomId: 'room@kapow.games',
  type: 'before', // To fetch 20 messages exchanged in the room since `1517568717299-n8e-m202`
  count: 20,
  onSuccess: function(packets) {
    console.log("Packets fetched from history", packets);
  },
  onFailure: function(error) {
    console.error(error);
  }
});
```
Used to fetch the packets that have been exchanged within a [room](#room) since the last time the game was open. The API returns a list of packets that have been exchanged in the group since the [packet](#packet) denoted by `packetId` in the success callback. More packets can be fetched recursively.

Parameter | Description
--------- | -----------
packetId | Identifier for the packet before/after which you want to fetch packets. Skip this attribute to get the history from the beginning.
roomId | Identifier for the room whose history is to be fetched.
type | Possible values: `before`/`after`.
count | Number of packets you want to retrieve (maximum of 25). Default value is `25`.

Response Parameter | Description
------------------ | -----------
packets | Array of [packets](#packet) exchanged in the room.

# Invoke Server Function
```javascript
kapow.rpc.invoke({
  functionName: 'functionName',
  parameters: {
    'key': 'value'
  },
  invokeLazily: true,
  onSuccess: function(response) {
    if (response.status == "executed") {
      console.log("Result of invoking server side function: ", response.result);
    } else { // response.status == "scheduled"
      console.log("Function scheduled for invocation.");
    }
  },
  onFailure: function(response) {
    console.error("Error while invoking server side function: ", response.error);
  }
});
```
> Sample server side function:

```javascript
var game = {};

game.makeMove = function(move) {
  if (isValid(move)) {
    broadcastMove(move, function() {
    	// Success callback
    	kapow.return(true);
    });
  } else {
    kapow.return(false);
  }
}
```
> Sample client side code:

```javascript
kapow.rpc.invoke({
  functionName: 'makeMove',
  parameters: move,
  invokeLazily: false,
  onSuccess: function(response) {
    if (response.result) {
      console.log("Valid move");
    } else {
      console.error("Invalid move");
    }
  },
  onFailure: function(response) {
    console.error("Error: ", response.error);
  }
});
```

Invokes the specified method within your server JavaScript file and passes the value you return from your function in the success callback.

Parameter | Description
--------- | -----------
functionName | Name of the function on the server that you want to invoke.
parameters | JSON object you want to pass to the function as parameter during invocation.
invokeLazily | Set to `true` if you don't want to block on this network call and instead want the method to be invoked as and when the user has network connectivity. Default value is `false`.

Response Attribute | Description
------------------ | -----------
status | Indicates whether the request was `executed` or just `scheduled` (in case of lazily-invoked RPCs).
result | Response returned from the server.
error | Error message returned from the server.

<aside class="notice">
The method on the server side that is to be invoked must be part of a globally accessible `game` object.
</aside>

# Analytics
```javascript
kapow.analytics.sendEvent({
  eventName: 'new_game_tapped',
  attributes: {
    source: 'homescreen'
  }
});
```
Sends out an analytics event to Kapow servers. In future, there'll be a section in the Developer Dashboard where these events can be queried and visualized.

Parameter | Description
--------- | -----------
eventName | Name of the event to be sent.
attributes | JSON map of extra attributes `{'key':'value'}` that is to be sent along with the event.

# Stores
Use these stores instead of `localStorage` for consistent storage behavior across devices and operating systems.

<aside class="notice">
If the `key` is missing in store, the success callback is invoked with a `null` value.
</aside>

## Game Store
```javascript
kapow.gameStore.set({
  key: 'key',
  value: value,
  onSuccess: function() {
    console.log("Successfully stored value");
  },
  onFailure: function(error) {
    console.error("Error while storing value", error);
  }
});

kapow.gameStore.get({
  key: 'key',
  onSuccess: function(value) {
    console.log("Successfully retrieved value", value);
  },
  onFailure: function(error) {
    console.error("Error while retrieving value", error);
  }
});
```
`gameStore` is used to store and retrieve data that is shared across multiple rooms. For example, user preferences such as in-game-sound settings (muted or not).

## Room Store
```javascript
kapow.roomStore.set({
  key: 'key',
  value: value,
  onSuccess: function() {
    console.log("Successfully stored value");
  },
  onFailure: function(error) {
    console.error("Error while storing value", error);
  }
});

kapow.roomStore.get({
  key: 'key',
  onSuccess: function(value) {
    console.log("Successfully retrieved value", value);
  },
  onFailure: function(error) {
    console.error("Error while retrieving value", error);
  }
});
```
`roomStore` is used to store and retrieve data that is specific to a particular room.

# Information

## Get Player Information
```javascript
kapow.getPlayer({
  playerId: 'alice@kapow.games',
  onSuccess: function(player) {
    console.log("Player information", player);
  },
  onFailure: function(error) {
    console.error("Error fetching player information", error);
  }
});
```
Returns information regarding the player, denoted by `playerId`.

Parameter | Description
--------- | -----------
playerId | Identifier of the player whose information you want to retrieve.

### Player
> Sample Player Object:

```json
{
  id: "alice@kapow.games",
  firstName: "Alice",
  lastName: "Michael",
  profileImage: "https://kapow.games/alice.jpg",
  affiliation: "invited",
  country: "IN"
}
```

Attribute | Description
---------------- | -----------
id | Identifier of the player whose information was retrieved.
firstName | First name of the player.
lastName | Last name of the player.
profileImage | URL to the user's profile image.
affiliation | User's affiliation in the room. Possible values: `invited`/`accepted`/`left`/`rejected`/`unknown`.
country | ISO Alpha 2 code of the country that the user belongs to.


## Get User Information
```javascript
kapow.getUser({
  onSuccess: function(user) {
    console.log("User information", user);
  }
});
```
Returns information regarding the user whose device the game is running on.

### User
> Sample User Object:

```json
{
  player: {
    id: "alice@kapow.games",
    name: "Alice Michael",
    profileImage: "https://kapow.games/alice.jpg",
    affiliation: "invited"
  }
}
```

Attribute | Description
---------------- | -----------
player | [Player](#player) object corresponding to the user.

## Get Room Information
```javascript
kapow.getRoom({
  onSuccess: function(room) {
    console.log("Room information", room);
  },
  onFailure: function(error) {
    console.error("Error while fetching room information", error);
  }
});
```
Returns information regarding the room that's loaded in the WebView.
If a [room](#room) has not been created/loaded yet, you'll receive an error response.

### Room
> Sample Room Object:

```json
{
  id: "room@kapow.games",
  type: "solo",
  nextPlayerId: "bob@kapow.games",
  players: [{
    name: "Alice",
    id: "alice@kapow.games",
    profileImage: "https://example.com/alice.jpg",
    affiliation: "accepted"
  }, {
    name: "Bob",
    id: "bob@kapow.games",
    profileImage: "https://example.com/bob.jpg",
    affiliation: "accepted"
  }]
}
```

Attribute | Description
---------------- | -----------
id | Identifier of the room.
type | Indicates the type of the room. Possible values are: `solo`/`friends`/`random`.
nextPlayerId | Identifier for the player whose turn it is next.
players | Array of [player](#player) objects who are part of the room.

# Social Sharing
```javascript
kapow.social.share({
  text: "Hello, world!",
  medium: "facebook",
  onSuccess: function() {
    console.log("Successfully shared");
  },
  onFailure: function(error) {
    console.error("Error while sharing", error);
  }
});
```

Shares the `text` on the specified `medium`.

Parameter | Description
--------- | -----------
text | Text to be shared.
medium | Medium on which you want to share the post. Either of `facebook`/`twitter`. Skip this attribute if you want Kapow to pop up the generic share dialog.

# Scoreboard Display
```javascript
kapow.boards.displayScoreboard({
  metric: 'coins',
  interval: 'alltime',
  onDismiss: function() {
    console.log("Scoreboard dismissed");
  }
});
```

Displays the scoreboard for the specified `metric` and `interval`.

Parameter | Description
--------- | -----------
metric | Configured metric whose stats are to be displayed.
interval | Time interval for which the stats are to be displayed. Possible values are: `daily`/`weekly`/`monthly`/`alltime`.

# WebView Management

## Close WebView
```javascript
kapow.close();
```
Closes the web-view and sends the user to the home-screen of Kapow.
