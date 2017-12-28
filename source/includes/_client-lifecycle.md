# Lifecycle Events
<aside class="notice">
Each of these functions is to be defined inside the scope of a globally accessible `game` object.
</aside>

## onLoad
```javascript
game.onLoad = function(room) {
  if (room) {
    startGamePlay(room);
  } else {
    displayHomeScreen();
  }
}
```
Called when the game is loaded, along with a `room` object (which can be `null` in case a room hasn’t been created yet).
Your game's code should only start executing from this point. Treat this as the equivalent of `document.onload`.

## onPause
```javascript
game.onPause = function() {
  muteSounds();
  pauseGame();
}
```
Called when the game loses focus (the app is put in the background). This is an indicator for the game developer to persist the game’s state in the `roomStore`.

## onResume
```javascript
game.onResume = function() {
  unmuteSounds();
  resumeGame();
}
```
Called when the game gains focus.

## onDataReceived
```javascript
game.onDataReceived = function(packet) {
  var move = packet.data;
  updateGameState(move);
}
```
Called when one of the players sends out data.

## onTurnChange
```javascript
game.onTurnChange = function(player) {
  kapow.getUserInfo(function(user) {
    if (user.player.id === player.id) {
      displayOwnTurn();
    } else {
      displayOpponentsTurn();
    }
  });
}
```
Called as a result of a `setNextPlayer` API call. Indicates who the next player to make the move is. You can compare this with the user's own player-id to check if it's his turn currently.

## onPlayerJoined
```javascript
game.onPlayerJoined = function(player) {
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
}
```
Called when a new/invited player joins/accepts an invitation to the room.

## onBackButtonPressed
```javascript
game.onBackButtonPressed = function() {
	if (isUserOnGamePlayScreen()) {
		showConfirmationDialog();
		return true;
	} else {
		return false;
	}
}
```
Called when the user presses the back-button. 
Return `true` if you are handling this particular event on your own. If not, Kapow will close the web-view.

## onGameEnd
```javascript
game.onGameEnd = function(outcome) {
  displayOutcomeScreen(outcome);
}
```
Called when the server signals the end of a game.

## onGameExpiry
```javascript
game.onGameExpiry = function() {
  showGameExpiredScreen();
}
```
Called when the invite to a friend/random opponent expires. The room will be closed post this.
