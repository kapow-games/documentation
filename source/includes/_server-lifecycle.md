# Lifecycle events
<aside class="notice">
Each of these functions is to be defined inside the scope of a globally accessible `game` object.
</aside>

## onPlayerJoined
```javascript
game.onPlayerJoined = function(player) {
  doStuffOnPlayerJoin(player);
}
```
Called when a new/invited player joins/accepts an invitation to the room.

## onPlayerLeft
```javascript
game.onPlayerLeft = function(player) {
  doStuffOnPlayerLeave(player);
}
```
Called when an existing player leaves.

## onInviteRejected
```javascript
game.onInviteRejected = function(player) {
  doStuffOnInviteRejected(player);
}
```
Called when an invited player rejects the invite.

# Storage
On demand, we'll provision a DynamoDB table or a MySQL database for you that can be used for storing and retrieving data.

# Libraries
These third-party libraries are at your disposal:
* [request](https://github.com/request/request)
* [sql](https://github.com/mysqljs/mysql)

You can include them with a `require('mysql')` command.
