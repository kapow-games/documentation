
# Broadcast Data

## Send Turn
```javascript
kapow.game.sendTurn({
  data: {
    move: 'x'
  },
  roomId: 'room@kapow.games',
  senderId: 'alice@kapow.games',
  nextPlayerId: 'bob@kapow.games',
  successCb: function() {
    console.log("Successfully sent data");
  },
  failureCb: function(error) {
    console.error("Something went wrong", error);
  }
});
```
Broadcasts `data` to all players in the room on behalf of `fromPlayerId` as a move (triggering the `data` event for every player on the client), sets the next player to `nextPlayerId`, and sends him a [notification](#notification) if specified.

Parameter | Description
--------- | -----------
data | Data to be broadcasted within the room.
roomId | Identifier for the room in which the data to be broadcasted.
senderId | Identifier for the player on behalf of whom the data is to be broadcasted.
nextPlayerId | Player whose turn it is next.
notification | Optional [notification](#notification) that should be sent to the player identified by `nextPlayerId`.

## Send Data
```javascript
kapow.game.sendData({
  data: {
    move: 'x'
  },
  senderId: 'alice@kapow.games',
  roomId: 'room@kapow.games',
  successCb: function() {
    console.log("Successfully sent data");
  },
  failureCb: function(error) {
    console.error("Something went wrong", error);
  }
});
```
Broadcasts data to all players in the room on behalf of the specified player as a move. This triggers the `data` event for every player on the client.

Parameter | Description
--------- | -----------
data | Data to be broadcasted within the room.
roomId | Identifier for the room in which the data to be broadcasted.
senderId | Identifier for the player on behalf of whom the data is to be broadcasted.

# Room Management
## Set Next Player
```javascript
kapow.game.setNextPlayer({
  playerId: 'bob@kapow.games',
  roomId: 'room@kapow.games',
  successCb: function() {
    console.log("Successfully set the next player");
  },
  failureCb: function(error) {
    console.error("Something went wrong", error);
  }
});
```
Tells every player in the room whose turn it is next. This triggers the `turn_change` event on the client side.

Parameter | Description
--------- | -----------
playerId | Identifier for the player whose turn it is next.
roomId | Identifier for the room that the player is a part of.

## End Game
```javascript
kapow.game.end({
  outcomeType: 'result'
  ranks: {
    'alice@kapow.games': 1,
    'bob@kapow.games': 2
  },
  data: {
    key: 'value'
  },
  roomId: 'room@kapow.games',
  onSuccess: function() {
    console.log("Successfully ended game");
  },
  onFailure: function(e) {
    console.error("Game could not be ended", e);
  }
});
```
Triggers the `game_end` callback within the room with the passed outcome object and saves it on Kapow servers. Any extra data that you want the clients to receive within this callback can be set inside the `data` field.

Parameter | Description
--------- | -----------
outcomeType | Type of outcome. Possible values are: `result`/`resignation`/`draw`/`timeout`.
ranks | Map of player identifiers to their respective ranks.
data | Extra data that you want to transmit along with the outcome.
roomId | Identifier of the room where the game is to be ended.

# Utilities

## Schedule RPC
```javascript
kapow.rpc.schedule({
  roomId: 'room@kapow.games',
  functionName: 'sendNotification',
  parameter: {
    user: 'bob@kapow.games'
  },
  delayInMinutes: 5,
  successCb: function() {
    console.log("Successfully scheduled RPC");
  },
  failureCb: function(e) {
    console.error("Error while scheduling RPC", e);
  }
});
```
Schedules invocation of a function in your server side code, within a globally defined `game` object, with a specified parameter, after a specific time interval, in the context of a room (if necessary). It's equivalent to invoking `game.functionName(parameter)` after a set-timeout.

Parameter | Description
--------- | -----------
roomId | Identifier for the room in the context of which you want this function to be invoked. Optional parameter.
functionName | Name of the function to be invoked.
parameter | Parameter to be passed to the specified function during invocation.
delayInMinutes | Amount of time, in minutes, post which the function is to be invoked. Supports only whole numbers.

## Send Notification
```javascript
kapow.sendNotification({
  players: ['alice@kapow.games', 'bob@kapow.games'],
  notification: notification,
  successCb: function() {
    console.log("Successfully sent notification");
  },
  failureCb: function(e) {
    console.error("Error sending notification", e);
  }
});
```
Sends a notification to the list of specified players.

Parameter | Description
--------- | -----------
players | List of player identifiers to whom the notification is to be sent.
notification |[Notification](#notification) to be sent.

### Notification
> Sample notification:

```javascript
{
  id: 'nudge_roomId',
  title: 'Y U NO play?',
  text: 'Time is running out!', ,
  buttons: [{
    text: 'Play',
    action: 'kapow://room/open?roomId=' + kapow.getRoom().id;
  }]
}
```
Parameter | Description
--------- | -----------
id | Unique identifier for the notification. If specified, will replace a previous notification generated by the same `id` instead of creating a duplicate notification on the device.
title | Title of the notification.
text | Text to be sent with the notification.
buttons | Array of [buttons](#buttons) that are to be displayed on the device. Maximum array size is 3.

#### Buttons
Parameter | Description
--------- | -----------
text | Button text.
action | [Action](#action) to be performed on clicking the button.

#### Action
Available actions are as follows:

* Open game: `kapow://game/open?gameId=<gameId>`
* Open a specific room: `kapow://room/open?roomId=<roomId>`
* Create a new room with a set of players: `kapow://room/create?gameId=<gameId>&playerIds=[player_1_id,player_2_id]`
* External links: `https://yoursite.com`

# Information

## Get Player Information
```javascript
var player = kapow.getPlayer();
```
Returns information regarding the [player](#player) who invoked the server side code.

### Player
> Sample Player Object:

```json
{
  id: "alice@kapow.games",
  firstName: "Alice",
  lastName: "Michael",
  profileImage: "https://kapow.games/alice.jpg",
  affiliation: "accepted",
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

## Get Room Information
```javascript
var room = kapow.getRoom()
```
Returns information regarding the [room](#room) in whose context the server side code was invoked.

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

# Feeds
```javascript
kapow.feeds.post({
  playerId: 'alice@kapow.games',
  templateValues: {
    player: 'Alice',
    coins: '100',
  },
  successCb: function() {
    console.log("Successfully posted to feed");
  },
  failureCb: function(e) {
    console.error("Error posting to feed", e);
  }
});
```
Applies the specified `[templateValues](#template-values)` to the game specific template, and publishes it on the specified player's feed.

Parameter | Description
--------- | -----------
playerId | Player on whose feed the post is to be made.
[templateValues](#template-values) | JSON map of values that are to be used to template the feed-post string.

## Template Values
> Example:

```javascript
{
  'player': 'Alice',
  'level': '10',
  'starCount': 3
}
```
For a post like `{player} cleared level {level} with {starCount} stars.`, the `templateValues` will look as follows.

# Scoreboards
```javascript
kapow.boards.postScores({
  playerId: 'alice@kapow.games',
  scores: {
    gold: 50,
    points: 100,
    time: 43.21
  },
  successCb: function() {
    console.log("Successfully posted scores");
  },
  failureCb: function(e) {
    console.error("Error posting scores", e);
  }
});
```
Posts the passed set of scores to Kapow's scoreboards for the specified player.

Parameter | Description
--------- | -----------
playerId | Player for whom the scores are to be posted.
scores | JSON map of `metric` to corresponding score/value.

# Analytics
```javascript
kapow.analytics.sendEvent({
  eventName: 'outcome_reached',
  attributes: {
    outcomeType: 'draw'
  }
});
```
Sends out an analytics event to Kapow servers. In future, there'll be a section in the Developer Dashboard where these events can be queried and visualized.

Parameter | Description
--------- | -----------
eventName | Name of the event to be sent.
attributes | JSON map of extra attributes `{'key':'value'}` that is to be sent along with the event.

# Response
```javascript
kapow.return({
  response: response,
  error: error
});
```
> Sample success response:

```javascript
kapow.return({
  response: {
    key: 'value'
  }
});
```
> Sample error response:

```javascript
kapow.return({
  error: {
    key: 'value'
  }
});
```

Returns the specified `response` attribute as a response in the `successCb` of the client that invoked the server side function. If an `error` attribute is specified, it's assumed that the invocation resulted in an error and the corresponding `failureCb` of the client is called.

Parameter | Description
--------- | -----------
response | Success response of the invocation that is to be returned to the client.
error | Error response of the invocation that is to be returned to the client.