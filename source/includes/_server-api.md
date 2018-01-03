
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
