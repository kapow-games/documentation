# Events

## Player Join
```javascript
kapow.on('player_join', function(player) {
  var room = kapow.getRoom();
  var countOfAcceptedPlayers = 0;
  room.players.forEach(function(player) {
    if (player.affiliation == "accepted") {
      countOfAcceptedPlayers++;
    }
  });
  if (countOfAcceptedPlayers == EXPECTED_COUNT_OF_PLAYERS) {
    startGame(room);
  }
});
```
Published when a new/invited player joins/accepts an invitation to the room.

## Player Leave
```javascript
kapow.on('player_leave', function(player) {
  var room = kapow.getRoom();
  var countOfAcceptedPlayers = 0;
  room.players.forEach(function(player) {
    if (player.affiliation == "accepted") {
      countOfAcceptedPlayers++;
    }
  });
  if (countOfAcceptedPlayers != EXPECTED_COUNT_OF_PLAYERS) {
    endGame(room);
  }
});
```
Called when an existing player leaves.

## Invite Reject
```javascript
kapow.on('invite_reject', function(player) {
  var room = kapow.getRoom();
  doStuffOnInviteRejected(player, room);
});
```
Called when an invited player rejects the invite.
