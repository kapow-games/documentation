---
title: Kapow Documentation

toc_footers:
  - <a href='./index.html'>Introduction</a>
  - <a href='./dashboard.html'>Developer Dashboard</a>
  - <a href='./client-api-reference.html'>Client API Reference</a>
  - <a href='./server-api-reference.html'>Server API Reference</a>

search: true
---

# Introduction

The Kapow iOS and Android mobile apps allow users to play games with their friends. There can be multiple game sessions (of the same, or of different games) active at any time. Each game session can have two or more users, including observers. The users can communicate with each other with an in-app chat as they are playing. Games can generate and send rich notifications to users.

Kapow games are HTML 5 games. The game developer does not have to adopt any new technology. The Kapow platform ensures that games run on a modern WebKit incarnation (think Safari and Chrome), so the game developer does not have to worry about compatibility with an unknown set of browsers.

The Kapow SDK provides JavaScript APIs tailored for writing games.

## Benefits

### High Level
The API primitives - "Game Data", "Player", "Room" - are designed to be at level of abstraction where the custom game logic code can directly work with them.
### Network
The SDK transparently handles peer endpoint discovery, network connectivity changes, and the actual network connections. Kapow servers buffer data to ensure that it gets delivered to players who are currently offline.
### Cheat Proof
The SDK enforces an architecture based on an untrusted client model. This ensures that no player can cheat by gaining an information advantage, even if they run a modified copy of the Kapow app.
### Hosting
Kapow takes care of hosting the game, and intelligently caching it on the user's device for responsiveness.
### Leaderboards, Replays, In-game chat
The Kapow app provides all the essentials that gamers have come to expect from good games, without requiring the game developer to write any custom code.

Unlike general purpose app stores which use coarse-grained statistics like the number of downloads, Kapow uses gaming related metrics for ranking games when listing them for the user. These rankings are personalized for each user based on her play history. Kapow's content recommendation engine can use the genre specific metadata for each game. All these result in improved game discovery for the user and improved discoverability for game developers.

To summarize, Kapow lets game developers focus on the game content by taking care of the infrastructure tasks, and provide them with a platform where they can grow viral simply by the virtue of their content.
