# Start Game

## Start Solo Game
```javascript
kapow.startSoloGame(function(room) {
  console.log("Solo room created", room);
  // Start game play here
}, function(error) {
  console.error("Something went wrong", error);
});
```
Returns a `Room` generated offline. You can store this identifier in your game-store for future use-cases.

## Start Game With Friends
```javascript
kapow.startGame(playerIdList, function(room) {
  console.log("Room with friends created", room);
  // Start game play here
}, function(error) {
  console.error(error);
});
```
Creates a `Room` with the mentioned players, and sends out an invitation to all of them.

Parameter | Description
--------- | -----------
playerIdList | An array of player-ids with whom you want to create a room. Eg. `[player_1@kapow.games, player_2@kapow.games]`

## Choose Friends To Start A Game With
```javascript
kapow.startGameWithFriends(minimumNumberOfPlayers, maximumNumberOfPlayers, function(room) {
  console.log("Room with chosen friends created", room);
  // Start game play here
}, function(error) {
  console.error(error);
});
```
Pops up an overlay asking the user to select a specific number of his friends to start a game with, creates and returns a new room with the selected players.

Parameter | Description
--------- | -----------
minimumNumberOfPlayers | Minimum number of players the user must select to create the room.
maximumNumberOfPlayers | Maximum number of players the user is allowed to select.

## Start Game With Random Players
```javascript
kapow.startGameWithRandomPlayers(attributes, function(room) {
  console.log("Room with random players created", room);
  // Start game play here
}, function(error) {
  console.error(error);
});
```
Creates a room with `attributes`. When another player tries to start a game against random players with the same set of `attributes`, Kapow will add that player to this room.

Parameter | Description
--------- | -----------
attributes | A JSON object that'll be used to match players. Eg: `{'difficultyLevel': 'medium'}`.

## Invite player
```javascript
kapow.invitePlayers(minimumNumberOfPlayers, maximumNumberOfPlayers, function(room) {
  console.log("Room with invited players", room);
  // Resume game play here
}, function(error) {
  console.error(error);
});
```
Displays the native overlay to select and add players to the active room. Will return the updated `Room` in the success callback.

Parameter | Description
--------- | -----------
minimumNumberOfPlayers | Minimum number of players the user must select to create the room.
maximumNumberOfPlayers | Maximum number of players the user is allowed to select.

## Rematch
```javascript
kapow.rematch(function(room) {
  console.log("Rematch room created", room);
  // Start game play here
}, function(error) {
  console.error(error);
});
```
Creates a new `Room` with the same set of players as there are in the existing one, and sends out an invitation to all of them, ensuring that even if all players in the room request for a rematch simultaneously, only a single `Room` is created for all of them.

# End Game
<aside class="notice">
Only solo games can be ended from the client. To end a multiplayer game, the `endGame` API needs to be triggered from the server.
</aside>
## End Solo Game
```javascript
kapow.endSoloGame(function() {
  console.log("Solo game ended");
  // Show the game-ended screen
}, function(error) {
  console.error(error);
});
```
Tells Kapow that the solo game is over, and the app will stop listing the game in its list of currently active rooms.

# History
To fetch the moves that have been exchanged within a `Room` since the last time the game was open, either of the following two APIs can be used. Each returns a list of messages that have been exchanged in the group since the `message` denoted by `messageId` in the success callback. This can be of multiple types as mentioned in the documentation. More messages can be fetched recursively.

Parameter | Description
--------- | -----------
messageId | The id of the message before/after which you want to fetch messages. Pass `null` to get the history from the beginning.
numberOfMessages | Number of messages you want to retrieve (maximum of 25).

## Fetch messages before
```javascript
kapow.fetchHistoryBefore(messageId, numberOfMessages, function(messages) {
  console.log("Messages fetched from history", messages);
}, function(error) {
  console.error(error);
});
```
Returns a list of messages that were exchanged in the room before the specified `messageId`.

## Fetch messages after
```javascript
kapow.fetchHistoryAfter(messageId, numberOfMessages, function(messages) {
  console.log("Messages fetched from history", messages);
}, function(error) {
  console.error(error);
});
```
Returns a list of messages that were exchanged in the room after the specified `messageId`.

# Invoke Server Function
```javascript
kapow.rpc.invoke({
    functionName: 'functionName',
    parameters: { 'key': 'value' },
    invokeLazily: true
  },
  function(response) {
    if (response.status == "executed") {
      console.log("Result of invoking server side function: ", response.result);
    } else { // response.status == "scheduled"
      console.log("Function scheduled for invocation.");
    }
  },
  function(response) {
    console.error("Error while invoking server side function: ", response.error);
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
    invokeLazily: false
  },
  function(response) {
    if (response.result) {
      console.log("Valid move");
    } else {
      console.error("Invalid move");
    }
  },
  function(response) {
    console.error("Error: ", response.error);
  });
```

Invokes the specified method within your server JavaScript file and passes the value you return from your function in the success callback.

Parameter | Description
--------- | -----------
functionName | The name of the function on the server that you want to invoke.
parameters | JSON object you want to pass to the function as parameter during invocation.
invokeLazily | Set to `true` if you don't want to block on this network call and instead want the method to be invoked as and when the user has network connectivity. Default value is `false`.

Response Attribute | Description
------------------ | -----------
status | Indicates whether the request was `executed` or just `scheduled` (in case of lazily-invokable RPCs).
result | The response returned from the server.
error | Error message returned from the server.

<aside class="notice">
The method on the server side that is to be invoked must be part of a globally accessible `game` object.
</aside>

# Analytics
```javascript
kapow.analytics.sendEvent('eventName', attributes);
```
Sends out an analytics event to Kapow servers. In future, there'll be a section in the Developer Dashboard where these events can be queried and visualized.

Parameter | Description
--------- | -----------
eventName | The name of the event to be sent.
attributes | A JSON map of extra attributes `{'key':'value'}` that need to be sent along with the event.

# Stores
Use these stores instead of `localStorage` for consistent storage behavior across devices and operating systems.

<aside class="notice">
If the `key` is missing in store, the success callback is invoked with a `null` value.
</aside>

## Game Store
```javascript
kapow.gameStore.set('key', value, function() {
  console.log("Successfully stored value");
}, function(error) {
  console.error("Error while storing value", error);
});

kapow.gameStore.get('key', function(value) {
	console.log("Successfully retrieved value", value);
}, function(error) {
	console.error("Error while retrieving value", error);
})
```
`gameStore` is used to store and retrieve data that is shared across multiple rooms. For example, user preferences such as in-game-sound settings (muted or not).

## Room Store
```javascript
kapow.roomStore.set('key', value, function() {
  console.log("Successfully stored value");
}, function(error) {
  console.error("Error while storing value", error);
});

kapow.roomStore.get('key', function(value) {
	console.log("Successfully retrieved value", value);
}, function(error) {
	console.error("Error while retrieving value", error);
})
```
`roomStore` is used to store and retrieve data that is specific to a particular room.

# Information

## Get User Information
```javascript
kapow.getUserInfo(function(user) {
  console.log("User information", user);
});
```
> Sample User Object:

```json
{
	player:	{
	    name: "Kapow User",
	    id: "kapow_user_identifier",
	    profileImage: "https://example.com/photo.jpg",
	    affiliation: "accepted"
	}
}
```

Returns information regarding the user whose device the game is running on.

## Get Player Information
```javascript
kapow.getPlayerInfo(playerId, function(player) {
  console.log("Player information", player);
});
```
> Sample Player Object:

```json
{
    name: "Kapow User",
    id: "kapow_user_identifier",
    profileImage: "https://example.com/photo.jpg",
    affiliation: "invited"/"accepted"/"left"/"rejected"/"unknown"
}
```

Returns information regarding the player, denoted by `playerId`.

Parameter | Description
--------- | -----------
playerId | The id of the player whose information you want to retreive.

## Get Room Information
```javascript
kapow.getRoomInfo(function(room) {
  console.log("Room information", room);
});
```
> Sample Room Object:

```json
{
  roomId: "room_identifier",
  state: "locked" / "unlocked",
  type: "solo" / "friends" / "random"
  nextPlayerId: "nextPlayer@kapow.games",
  players: [{
    name: "Player 1",
    id: "player_1@kapow.games",
    profileImage: "https://example.com/player_1.jpg",
    affiliation: "accepted"
  }, {
    name: "Player 2",
    id: "player_2@kapow.games",
    profileImage: "https://example.com/player_2.jpg",
    affiliation: "accepted"
  }]
}
```

Returns information regarding the room that's loaded in the WebView.
If a `room` has not been created yet, you'll receive a `null` response.

# Social Sharing
```javascript
kapow.social.share(text, medium, function() {
	console.log("Successfully shared", text);
}, function(error) {
	console.error("Error while sharing", error);
});
```

Shares the `text` on the specified `medium`.

Parameter | Description
--------- | -----------
text | The text to be shared.
medium | The medium on which you want to share the post. Either of `facebook`/`twitter`. Pass `null` if you want Kapow to pop up the generic share dialog.

# Scoreboard Display
```javascript
kapow.boards.displayScoreboard({
  'metric': 'coins',
  'interval': 'alltime'
});
```

Displays the scoreboard for the specified `metric` and `interval`.

Parameter | Description
--------- | -----------
metric | The configured metric whose stats are to be displayed.
interval | The time interval for which the stats are to be displayed. Possible values are: `daily`/`weekly`/`monthly`/`alltime`.

# Room Management

## Display Active Rooms
```javascript
kapow.displayActiveRooms();
```
Displays a pop up that shows the list of active rooms, ones where the game hasn't ended yet, that the user is a part of.

## Get Active Rooms
```javascript
kapow.getActiveRooms(function(rooms) {
  for (var roomIndex in rooms) {
    console.log("Room #" + roomIndex + 1 + ": ", rooms[roomIndex]);
  }
}, function(error) {
  console.error(error);
})
```
Returns the list of active rooms, ones where the game hasn't ended yet, that the user is a part of.

## Load Room
```javascript
kapow.loadRoom(roomId, function(room) {
  console.log("Loaded room: ", room);
  startGamePlay(room);
}, function(error) {
  console.error(error);
});
```
Loads the context of a `Room` indicated by the `roomId` to the WebView, granting access to all room specific APIs and attaching all related lifecycle callbacks.
For example: Call this when you want to take the user to the last room he was a part of (as fetched from `getActiveRooms`) on game load.

## Unload Room
```javascript
kapow.unloadRoom(function() {
  console.log("Unloaded room successfully");
  // Take the user to the game's home screen
}, function(error) {
  console.error(error);
});
```
Unloads the context of a `Room` from the WebView, removing access to all room specific APIs and detaching all related lifecycle callbacks.
For example: Call this when the user clicks on `Start a new game` while waiting to get matched with a random opponent/shares an invite link and no one has joined yet.

# WebView Management

## Close WebView
```javascript
kapow.close();
```
Closes the web-view and sends the user to the home-screen of Kapow.
