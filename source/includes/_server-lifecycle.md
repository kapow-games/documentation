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

## onPlayerLeft()
```javascript
game.onPlayerLeft = function(player) {
  doStuffOnPlayerLeave(player);
}
```
Called when an existing player leaves.

## onInviteRejected(<Player>)
```javascript
game.onInviteRejected = function(player) {
  doStuffOnInviteRejected(player);
}
```
Called when an invited player rejects the invite.
