
# Broadcast Data

## Send Turn
```javascript
kapow.game.sendTurn(data, roomId, fromPlayerId, nextPlayerId, notification, function() {
	console.log("Successfully sent data");
}, function(error) {
	console.error("Something went wrong", error);
});
```
Broadcasts `data` to all players in the room on behalf of `fromPlayerId` as a move (triggering the `onDataReceived(message)` function for every player), sets the next player to `nextPlayerId`, and sends him a notification if specified. If you want to skip the part where Kapow sends the next player a notification, you can pass `null` in place of the notification parameter.

Parameter | Description
--------- | -----------
data | Data to be broadcasted within the room.
roomId | Identifier for the room in which the data to be broadcasted.
fromPlayerId | Player on behalf of whom the data is to be broadcasted.
nextPlayerId | Player whose turn it is next.
notification | Notification that should be sent to the player identified by `nextPlayerId`.

## Send Data
```javascript
kapow.game.sendData(data, fromPlayerId, roomId, function() {
	console.log("Successfully sent data");
}, function(error) {
	console.error("Something went wrong", error);
});
```
Broadcasts data to all players in the room on behalf of the specified player as a move. This triggers the onDataReceived(message) function for every player.

Parameter | Description
--------- | -----------
data | Data to be broadcasted within the room.
fromPlayerId | Player on behalf of whom the data is to be broadcasted.
roomId | Identifier to the room in which the data to be broadcasted.

# Room Management
## Set Next Player
```javascript
kapow.setNextPlayer(playerId, roomId, function() {
	console.log("Successfully set the next player");
}, function(error) {
	console.error("Something went wrong", error);
});
```
Tells every player in the room whose turn it is next. This triggers the `onTurnChanged(playerId)` function on the client side.

Parameter | Description
--------- | -----------
playerId | Identifier for the player whose turn it is next.
roomId | Identifier for the room that the player is a part of.

## End Game
```javascript
kapow.game.end(outcome, roomId, successCb, failureCb);
```
> Example:

```javascript
kapow.game.end({
	'type': 'result'
	'ranks': {
		'player_1_id': 1,
		'player_2_id': 2
	},
	'data': {
		'extraData': 'toBeBroadcasted'
	}
}, 'room@groups.kapow.games', function() {
	console.log("Successfully ended game");
	kapow.return({
		"type": "success"
	});
}, function(e) {
	console.error("Game could not be ended", e);
	kapow.return(null, {
		"type": "error"
	});
});
```
Triggers the `onGameEnd` callback within the room with the passed outcome object and saves it on Kapow servers. Any extra data that you want the clients to receive within this callback can be set inside the data field of the outcome object.

Parameter | Description
--------- | -----------
type | Type of outcome. Possible values are: `result`/`resignation`/`draw`/`timeout`.
ranks | Map of player identifiers to their respective ranks.
data | Extra data that you want to transmit along with the outcome.

## Schedule RPC
```javascript
kapow.rpc.schedule(functionName, parameter, delayInMinutes, function() {
	console.log("Successfully scheduled RPC");
}, function(e) {
	console.error("Error while scheduling RPC", e);
});
```
Schedules invocation of a function referenced by `functionName` in your server side code, within a globally defined `game` object, with the specified `parameter`, after the specified time `delay` in minutes. It's equivalent to invoking `game.functionName(parameter)` after a set-timeout.

Parameter | Description
--------- | -----------
functionName | Name of the function to be invoked.
parameter | Parameter to be passed to the specified function during invocation.
delayInMinutes | Amount of time, in minutes, post which the function is to be invoked.

## Schedule RPC for Room
```javascript
kapow.rpc.scheduleForRoom(roomId, functionName, parameter, delayInMinutes, function() {
	console.log("Successfully scheduled RPC");
}, function(e) {
	console.error("Error while scheduling RPC", e);
});
```
Schedules invocation of a function referenced by `functionName` in your server side code, within a globally defined `game` object, with the specified `parameter`, after the specified time `delay` in minutes, in the context of the room specified by `roomId`. It's equivalent to invoking `game.functionName(parameter)` after a set-timeout.

Parameter | Description
--------- | -----------
roomId | Room in the context of which you want this function to be invoked.
functionName | Name of the function to be invoked.
parameter | Parameter to be passed to the specified function during invocation.
delayInMinutes | Amount of time, in minutes, post which the function is to be invoked.

## Send Notification
```javascript
kapow.sendNotification(playerId, notification, successCb, failureCb);
```
> Example:

```javascript
kapow.sendNotification('player@kapow.games', {
	'id': 'unique_identifier', // Optional
	'title': 'My Awesome Game',
	'text': 'Hello, World!',
	'buttons': [{
		'text': 'Ok',
		'action': 'action'
	}, {
		'text': 'Cancel',
		'action': 'action'
	}]
}, function() {
	console.log("Successfully sent notification");
}, function(e) {
	console.error("Error sending notification", e);
});
```
Sends a notification to the specified player.

Parameter | Description
--------- | -----------
playerId | Identifier for the player to whom the notification is to be sent.
notification | The notification to be sent.

## Get Player ID
```javascript
var playerId = kapow.getPlayerId();
```
Returns the identifier of the player who invoked the RPC.

## Get Room Info
```javascript
var roomInfo = kapow.getRoomInfo()
```
Returns information regarding the room in whose context the server side code was invoked. The returned object will contain an array called `playerInfos` which will contain a list of Player objects and an array called players which will contain a list of player-ids.

## Post to Feed
```javascript
kapow.feeds.post(feedpost, successCb, failureCb);
```
> Example

```javascript
kapow.feeds.post({
	'playerId': 'dave@kapow.games',
	'templateValues': {
		'player': 'Dave',
		'level': '10',
		'starCount': 3
	}
}, function() {
	console.log("Successfully posted to feed");
}, function(e) {
	console.error("Error posting to feed", e);
});
```
Applies the passed FeedPost object's templateValues to the game specific template, and publishes it to the specified player's feed.

## Post to Scoreboard
```javascript
kapow.boards.postScores(scores, successCb, failureCb);
```
> Example

```javascript
kapow.boards.postScores({
	'playerId': 'player@kapow.games',
	'scores': {
		'gold': 50,
		'points': 100,
		'time': 43.21
	}
}, function() {
	console.log("Successfully posted scores");
}, function(e) {
	console.error("Error posting scores", e);
});
```
Posts the passed set of scores to Kapow's leaderboards for the specified player.

## Analytics
```javascript
kapow.analytics.sendEvent('eventName', {
	'attribute': 'map'
});
```
Sends out an analytics event to our servers. We'll provide a section in the Developer Dashboard where these can be queried and visualized.

## Return Response
```javascript
kapow.return(response, error);
```
Returns data as a response to the client that invoked the server side function. If there is an error, you can call `kapow.return(null, error)` to invoke the failure callback on the client.
