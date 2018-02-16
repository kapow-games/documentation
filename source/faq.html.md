---
title: Kapow Documentation

language_tabs: # must be one of https://git.io/vQNgJ
  - javascript

toc_footers:
  - <a href='./index.html'>Introduction</a>
  - <a href='./dashboard.html'>Developer Dashboard</a>
  - <a href='./client-api-reference.html'>Client API Reference</a>
  - <a href='./server-api-reference.html'>Server API Reference</a>
  - <a href='./debugging.html'>Debugging</a>
  - <a href='./faq.html'>FAQ</a>

---

# Progress Dialogs
```javascript
game.makeMove = function(move) {
  showProgressDialog();
  kapow.rpc.invoke({
  functionName: 'validateAndBroadcastMove',
  parameters: {
    'move': move
  },
  invokeLazily: false,
  onSuccess: function(response) {
    console.log("Result of invoking server side function: ", response.result);
    hideProgressDialog();
    updateMoveOnUi(move);
  },
  onFailure: function(response) {
    console.error("Error while invoking server side function: ", response.error);
    hideProgressDialog();
    if (error.cause == 'network_timeout') {
      showRetryDialog();
    } else {
      showGenericErrorDialog();
    }
  }
});
```
To give a good experience to the users while you are making a blocking RPC invocation, ensure that there's some form of progress state that's displayed to the user.

# Restoring Game State
Every time the `pause` event is triggered, you should dump the entire game state into the `roomStore`, and on `load`/`resume`, fetch this state from the `roomStore` and setup the interface appropriately. In addition to this, you should also call `kapow.history.fetch` with the last `packetId` you have seen.

# Nudges & Turn-Expiry

> Pseudo-code for implementing nudges:

```javascript
game.broadcastMove = function(data) {
  if (!isValid(data.move)) {
    kapow.return({
      error: {
        "cause": "invalid_move"
      }
    });
    return;
  }

  kapow.game.sendTurn({
    data: data.move,
    roomId: kapow.getRoom().id,
    senderId: data.playerId,
    nextPlayerId: opponentId,
    successCb: function() {
      scheduleNudge(data, opponentId);
    },
    failureCb: function(error) {
      console.error("Something went wrong", error);
      kapow.return({
        error: e
      });
    }
  });
}

var scheduleNudge = function(data, opponentId) {
  console.log("Successfully sent data");
  var timestamp = Date.now();
  db.storeTimestampAtWhichLastMoveWasPlayed(kapow.getRoom().id, data.playerId, timestamp);
  kapow.rpc.schedule({
    roomId: kapow.getRoom().id,
    functionName: 'notifyIfNecessary',
    parameter: {
      playerId: opponentId
    },
    delayInMinutes: ALLOWED_INACTIVITY_DURATION_IN_MINUTES,
    successCb: function() {
      console.log("Successfully scheduled RPC");
      kapow.return({
        response: "success"
      });
    },
    failureCb: function(e) {
      console.error("Error while scheduling RPC", e);
      kapow.return({
        error: e
      });
    }
  });
}

game.notifyIfNecessary = function(data) {
  db.getTimestampAtWhichLastMoveWasPlayed(kapow.getRoom().id, data.playerId, function(timestamp) {
    var currentTimestamp = Date.now();
    var hasPlayerMovedInRoomSinceTimestamp = (currentTimestamp - timestamp) < ALLOWED_INACTIVITY_DURATION_IN_MILLIS;
    if (!hasPlayerMovedInRoomSinceTimestamp) {
      sendNudge(data.playerId);
    }
  });
}

var sendNudge = function(playerId) {
  var notification = {
    'title': "Awesome Game",
    'text': "Play your turn!",
    'buttons': [{
      'text': 'Play',
      'action': 'kapow://room/open?roomId=' + kapow.getRoom().id
    }]
  };

  kapow.sendNotification({
    players: [playerId],
    notification: notification,
    successCb: function() {
      console.log("Successfully sent notification");
      kapow.return({
        response: "success"
      });
    },
    failureCb: function(e) {
      console.error("Error sending notification", e);
      kapow.return({
        error: e
      });
    }
  });
}

db.storeTimestampAtWhichLastMoveWasPlayed = function(roomId, playerId, timestamp) {
  var sql = mysql.createConnection({
    host: process.env.RDS_ENDPOINT,
    user: 'username',
    password: 'password',
    database: 'db_name'
  });
  sql.connect();
  sql.query('INSERT INTO `moves` (`room_id`, `player_id`, `timestamp`) VALUES ('
    roomId + ',' + playerId + ',' + timestamp + ') ON DUPLICATE KEY UPDATE ' +
    '`room_id`=values(`room_id`),' +
    '`player_id`=values(`player_id`),' +
    '`timestamp`=values(`timestamp`);');

  sql.end();
}

db.getTimestampAtWhichLastMoveWasPlayed = function(roomId, playerId, callback) {
  var sql = mysql.createConnection({
    host: process.env.RDS_ENDPOINT,
    user: 'username',
    password: 'password',
    database: 'db_name'
  });
  sql.connect();
  sql.query('SELECT `timestamp` FROM `moves` WHERE `room_id`="' + roomId + '" AND `player_id`="' + playerId + '";',
    function(error, results, fields) {
      callback(results[0].timestamp);
    }
  );
  sql.end();
}
```
On the server side:

1. Validate and broadcast the move the client sent you.
2. Store the timestamp at which the move was made by the player in the DB, in a table where a combination of `room_id` and `player_id` is the primary key.
3. Set the next player ID to the opponent's ID.
4. Schedule an RPC to be invoked after a specific period of time, for a function, which when invoked checks if the player has made a move between the time when the RPC was scheduled, and the time it was actually called.
5. If he has not made a move within the duration, send a notification.

Turn expiry works on the same principles, except that you call `kapow.game.end()` instead of sending out this notification.


# Leaderboards
## Step 1
Create a Board from the game details section of the [Developer Dashboard](https://dev.kapow.games).

<a href="https://f.kapowcdn.com/dafe7f11518779945304b2a7"><img src="https://f.kapowcdn.com/dafe7f11518779945304b2a7" height="300" width="200" ></a>

Parameters:

* Metric Name: Variable for which you'll be posting the scores for
* Display Name: Title of the metric which will be shown as the header on the scoreboards interface in-app
* Short Display Name: Title of the column in the scoreboard interface
* Intervals: Intervals for which you want the scoreboards to be re-computed
* Sorting Order: The order in which the posted metrics should be sorted
* Addable Metric: Whether Kapow should add up each posted score

## Step 2
```javascript
game.validateAndPostScores = function(score) {
  var playerId = score.playerId;
  var coins = score.coins;
  if (isValidScore(score)) { // Perform sanity checks to ensure that the score is achievable
    kapow.boards.postScores({
      playerId: playerId,
      scores: {
        'total_coins': coins
      },
      successCb: function() {
        console.log("Successfully posted scores");
        kapow.return({
          response: 'success'
        });
      },
      failureCb: function(e) {
        console.error("Error posting scores", e);
        kapow.return({
          error: e
        });
      }
    });
  } else {
    kapow.return({
      error: "invalid_score"
    });
  }
}
```
Add a function to validate and post the scores in your `server.js`.

## Step 3
```javascript
game.onLevelEnd = function(coins) {
  kapow.getUser({
    onSuccess: function(user) {
      kapow.rpc.invoke({
        functionName: 'validateAndPostScores',
        parameters: {
          'coins': coins,
          'playerId': user.player.id
        },
        invokeLazily: true
      });
    }
  });
}
```
Each time a player finishes a level, send the score to your server to be posted.

## Step 4
```javascript
game.showScoreboard = function() {
  kapow.boards.displayScoreboard({
    metric: 'total_coins',
    interval: 'alltime',
    onDismiss: function() {
      console.log("Scoreboard dismissed");
    }
  });
}
```

Finally, to display the following interface within the game call the `displayScoreboards` API.

<a href="https://f.kapowcdn.com/dafe7f1150849460878b5dcb"><img src="https://f.kapowcdn.com/dafe7f1150849460878b5dcb" height="350" width="200" ></a>


