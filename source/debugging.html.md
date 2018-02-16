---
title: Client API Reference

toc_footers:
  - <a href='./index.html'>Introduction</a>
  - <a href='./dashboard.html'>Developer Dashboard</a>
  - <a href='./client-api-reference.html'>Client API Reference</a>
  - <a href='./server-api-reference.html'>Server API Reference</a>
  - <a href='./debugging.html'>Debugging</a>
  - <a href='./faq.html'>FAQ</a>

---

# Debugging

## Setup Device / Emulator
  * Make sure you have an Android device with android version > 4.2
  * Make sure you have chrome installed: [https://www.google.com/chrome/](https://www.google.com/chrome/)
  * Enable developer mode on your device: [http://www.androidauthority.com/enable-developer-options-569223/](http://www.androidauthority.com/enable-developer-options-569223/)
  * Turn on the USB debugging option under Developer Options
  * Useful links: 
    * Genymotion (Android emulator): [https://www.genymotion.com/fun-zone/](https://www.genymotion.com/fun-zone/)

## Install Kapow beta
* Link : [https://betas.to/qqmYhdCH](https://betas.to/qqmYhdCH)
* Sign in to the app
  * Launch the app and complete the Facebook login

## Register a game with Kapow
* Head over to [https://dev.kapow.games](https://dev.kapow.games)
* Check out the documentation for [Developer Dashboard](./dashboard.html) for help

## Add yourself as a beta tester
  * Go to the game you have just created 
  * Switch to the Distribution tab
  * Register the number you have signed up on the Kapow app with as a tester
  * Similarly add the accounts you want to test the game with (useful for multiplayer games)

## Testing
  * Launch the app and switch to the All (games) tab
  * You should see your game in this list
  * Select your game and connect your device to the computer
  * Open chrome and in the address bar type: [`chrome://inspect/#devices`](chrome://inspect/#devices)
  * You should see the device you have just connected or your emulator
  * In case you don’t, make sure you have USB debugging enabled
  * You should see a row titled `<Your Game Name> <Your game link>`
  * Click on inspect below the title to open the debugging window
  * Switch to console tab where you can debug your game by logging on the console
  * Additionally you can also view the screen by toggling screencast (This is usually enabled by default)
