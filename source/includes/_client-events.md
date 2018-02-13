# Events
You can attach handlers to the following lifecycle events.
## Load
```javascript
kapow.on('load', function(room) {
  if (room) {
    startGamePlay(room);
  } else {
    displayHomeScreen();
  }
});
```
Published when the game is loaded, along with a `room` object (which can be `null` in case a room hasn’t been created yet).
Your game's code should only start executing from this point. Treat this as the equivalent of `document.onload`.

## Pause
```javascript
kapow.on('pause', function() {
  muteSounds();
  pauseGame();
});
```
Published when the game loses focus (the app is put in the background). This is an indicator for the game developer to persist the game’s state in the `roomStore`.

## Resume
```javascript
kapow.on('resume', function() {
  unmuteSounds();
  resumeGame();
});
```
Called when the game gains focus.

## Incoming Data
```javascript
kapow.on('data', function(packet) {
  var move = packet.data;
  updateGameState(move);
});
```
> Sample packet:

```json
{
    packetId: "1518501780",
    data: {
      key: "value"
    },
    senderId: "alice@kapow.games"
}
```
Published when one of the players sends out data.

## Turn Change
```javascript
kapow.on('turn_change', function(player) {
  kapow.getUserInfo(function(user) {
    if (user.player.id === player.id) {
      displayOwnTurn();
    } else {
      displayOpponentsTurn();
    }
  });
});
```
Published as a result of a `setNextPlayer` API call. Indicates who the next player to make the move is. You can compare this with the user's own player-id to check if it's his turn currently.

## Player Join
```javascript
kapow.on('player_join', function(player) {
  kapow.getRoomInfo(function(room) {
    var countOfAcceptedPlayers = 0;
    room.players.forEach(function(player) {
      if (player.affiliation == "accepted") {
        countOfAcceptedPlayers++;
      }
    });

    if (countOfAcceptedPlayers == EXPECTED_COUNT_OF_PLAYERS) {
      startGamePlay(room);
    }
  });
});
```
Published when a new/invited player joins/accepts an invitation to the room.

## Game End
```javascript
kapow.on('game_end', function(outcome) {
  displayOutcomeScreen(outcome);
});
```
Published when the server signals the end of a game.

## onGameExpiry
```javascript
kapow.on('game_expire', function(outcome) {
  showGameExpiredScreen();
});
```
Published when the invite to a friend/random opponent expires. The room will be closed post this.

## Back Button Press
```javascript
kapow.on('back_button_press', function() {
  if (isUserOnGamePlayScreen()) {
    showConfirmationDialog();
    return true;
  } else {
    return false;
  }
});
```
Published when the user presses the back-button. 
Return `true` if you are handling this particular event on your own. If not, Kapow will close the web-view.

