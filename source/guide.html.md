---
title: Kapow Documentation

---

# API Guide

## Objects and Terminology
Each instance of a game has the following associated objects:

1. Room
2. Players
3. User
4. Message
5. Ranks

Each object has an associated unique ID, which you can use when storing state and referring to the objects in your own data.

All primitive fields in the objects are strings unless explicitly mentioned otherwise.

The [`Room`](https://github.com/kapow-games/docs/wiki/Objects#room) represents where the game is occurring. It consists of two or more [`Player`](https://github.com/kapow-games/docs/wiki/Objects#player)s. One of them is a special player representing the current device where the game is running and is called the [`User`](https://github.com/kapow-games/docs/wiki/Objects#user). Your game can broadcast [`Data`](https://github.com/kapow-games/docs/wiki/Objects#message) objects to communicate. Finally, when the game ends, your game code will provide a [`Ranks`](https://github.com/kapow-games/docs/wiki/Client-API-Reference#ranks) object which Kapow will use for generating rankings.

## Fundamentals
The Kapow SDK requires you to split your app into two components:

1. The web app that will be loaded into a web view on mobile clients
2. JavaScript code that will be uploaded to our servers and executed when needed

A couple of Kapow APIs can only be accessed by the latter, including the [`sendData`](https://github.com/kapow-games/docs/wiki/Server-API-Reference#senddatadata-successcb-failurecb) function which broadcasts data within a [`room`](https://github.com/kapow-games/docs/wiki/Objects#room). Under the premise that clients cannot be trusted, this contract enables server-side validation before actions altering the game state are made.

Your client can trigger the functions you have written on your server side and get their responses back by using the [`invokeRPC`](https://github.com/kapow-games/docs/wiki/Client-API-Reference#invokerpcmethodname-parameters-successcb-failurecb) API.

Worry not, we shall go through this in detail in the tutorial section. You should first acquaint yourself with the lifecycle of a game.

**Note that Kapow expects all of your [life-cycle events](https://github.com/kapow-games/docs/wiki/Client-API-Reference#lifecycle-events) to be defined in the scope of a global `game` object.**

## Game Lifecycle
Once your game has been loaded into view, the [`onLoad`](https://github.com/kapow-games/docs/wiki/Client-API-Reference#onloadroom) life cycle function is called by the SDK, along with a [`Room`](https://github.com/kapow-games/docs/wiki/Objects#room), if one exists. If not, you can create one by using one of two API functions:

1. [`startGameWithFriends`](https://github.com/kapow-games/docs/wiki/Client-API-Reference#startgamewithfriendsminimumnumberofplayers-maximumnumberofplayers-room-successcb-failurecb), giving the count of the number of players that your game supports. Kapow will then display an overlay that will allow the user to select a maximum of these many players from among his friends who are already on Kapow.

2. [`startGameWithRandomPlayers`](https://github.com/kapow-games/docs/wiki/Client-API-Reference#startgamewithrandomplayersattributes-room-successcb-failurecb), along with any custom attributes (eg. `{difficultyLevel: 'medium'}`) you might want the room to be created with. Kapow will then create a `room` with these attributes and add existing/requesting players who match these set of attributes.

Each of these will return a `Room` in its success callback.

### Room
Here is an example `Room` object:
```
{
     roomId: "unique identifier",
     state: "locked"/"unlocked",
     players: [
       ...
     ]
}
```
Your game can at any time obtain the `Room` object again by calling the [`getRoomInfo`](https://github.com/kapow-games/docs/wiki/Client-API-Reference#getroominforoom-successcb-failurecb) API. The `Room` object will be passed to the success callback.

### Player
The `Player` object represents a participant in the game. For example:
```
{
     id: "unique identifier",
     name: "John Doe",
     nick: "john007",
     profileImage: "https://example.com/images/user.jpg",
     affiliation: "invited" / "accepted" / "left" / "rejected" / "unknown"
}
```
The `name` is the full name of the user. The `nick` is the nickname of the user. The `profileImage` is the URL where the user's avatar can be retrieved from. The `affiliation` indicates if the player is currently available or not. 

When someone is invited to play a game (as a result of [`startGameWithFriends`](https://github.com/kapow-games/docs/wiki/Client-API-Reference#startgamewithfriendsnumberofplayers-room-successcb-failurecb)), the corresponding player has affiliation set to `invited`.

Each time a player accepts an invitation to join a room or is matched and added to a pre-existing room (in the case of a game with random players) the affiliation is set to `accepted` and the SDK will trigger your [`onPlayerJoined`](https://github.com/kapow-games/docs/wiki/Client-API-Reference#onplayerjoinedplayer) function.

If some player rejects the invite, then her affiliation will be set to `rejected`. At this time, Kapow will call the game's [`onInviteRejected`](https://github.com/kapow-games/docs/wiki/Client-API-Reference#oninviterejectedplayer) callback function for each existing player in the room.

If an `accepted` player leaves the game, then her affiliation will be set to `left`, and the [`onPlayerLeft`](https://github.com/kapow-games/docs/wiki/Client-API-Reference#onplayerleftplayer) callback function will be called for each existing player in the room.

In the case of a room with random players, as and when you have enough players to start the game, you can call the [`lockRoom`](https://github.com/kapow-games/docs/wiki/Client-API-Reference#lockroomsuccesscb-failurecb) API to prevent the addition of more players.

So, for example, consider the scenario where Alice starts the game on her device. From inside the game, she selects the option to play with friends. Because of this, the game calls the [`startGameWithFriends`](https://github.com/kapow-games/docs/wiki/Client-API-Reference#startgamewithfriendsminimumnumberofplayers-maximumnumberofplayers-room-successcb-failurecb) function (with the count of players set to 2). At this time, Kapow will allow Alice to select one friend from her friend list. Suppose Alice selects Bob. This will result in an invite being sent to Bob, and the success callback function that was passed will be invoked with the following `Room` object:
```
{
  roomId: "xxx",
  state: "unlocked",
  players: [
      {
          id: "yyy",
          name: "Alice",
          nick: "alICE",
          profileImage: "http://example.com/alice.png",
          affiliation: "accepted"
      },
      {
          id: "zzz",
          name: "Bob",
          nick: "bobby",
          profileImage: "http://example.com/bob.png",
          affiliation: "invited"
      }
  ]
}
```
### User
The [`User`](https://github.com/kapow-games/docs/wiki/Objects#user) object refers to Kapow user on whose device the game is currently running. It currently has only one field, which represents the [`Player`](https://github.com/kapow-games/docs/wiki/Objects#player) object corresponding to the user. In the future, we might provide extra information about the user. 
```
{
    player:  Player,
}
```
Your game can at any time obtain the `User` object again by calling the [`getUserInfo`](https://github.com/kapow-games/docs/wiki/Client-API-Reference#getuserinfouser-successcb-failurecb) API function. The `User` object will be passed to the success callback.

### Message
The [`Message`](https://github.com/kapow-games/docs/wiki/Objects#message) object refers to any arbitrary JSON data that your game wants to communicate with other instances of your game. You can do that by calling the [`sendData`](https://github.com/kapow-games/docs/wiki/Server-API-Reference#senddatadata-successcb-failurecb) API function. It takes a `data` argument which can any object (as long as it is JSON serializable). Kapow will broadcast this to the other players in the room, and each of them will have the [`onDataReceived`](https://github.com/kapow-games/docs/wiki/Client-API-Reference#ondatareceivedmessage) callback function invoked with a `Message` object that looks like this:
```
{
     messageId: "abc",
     type: "move",
     data: ...
}
```
The `messageId` is a unique identifier for the message. The data field is the same as the `data` argument to the `sendData` from the sending device.

Your game can fetch older messages by using the [`fetchHistorySince`](https://github.com/kapow-games/docs/wiki/Client-API-Reference#fetchhistorysincemessageid-numberofmessages-listmessage-successcb-failurecb) and the [`fetchHistoryBefore`](https://github.com/kapow-games/docs/wiki/Client-API-Reference#fetchhistorybeforemessageid-numberofmessages-listmessage-successcb-failurecb) API functions.

Apart from `move`s, the message history fetched will also include affiliation changes (`affiliation_change`), turn changes (`turn_change`), or the game outcome itself (`outcome`).

Your game can store any information in two databases: the [`gameStore`](https://github.com/kapow-games/docs/wiki/Client-API-Reference#gamestoregetkey-successcb-failurecb) and the [`roomStore`](https://github.com/kapow-games/docs/wiki/Objects#roomstoregetkey-successcb-failurecb). Each of these has `get` and `set` functions which you can use to store any arbitrary key-value pairs. You should use the `gameStore` for any information that is applicable to your game in general (e.g. any user preferences), and use the `roomStore` for any information that is applicable to a particular instance of the game.

On the server side, you'll have access to a [DynamoDB](https://aws.amazon.com/dynamodb/) table, where you are free to store whatever you want. To get this provisioned, please contact one of our developers.

### Ranks
Finally, you end the game by calling the [`endGame`](https://github.com/kapow-games/docs/wiki/Client-API-Reference#endgameranks-successcb-failurecb) API function. This takes one argument, which is of type [`Ranks`](https://github.com/kapow-games/docs/wiki/Client-API-Reference#ranks). `Ranks` is a map of player IDs along with their ranks. 
```
[
  'winner-player-id': 1,
  'second-place-player-id': 2,
  ...
]
```