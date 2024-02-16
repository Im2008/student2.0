---
layout: base
title: Final Blog
description: Final Blog that teaches what a person has done over the past weeks.
type: collab
courses: { csse: {week: 24} }
---

<h2> Accomplished so far </h2>

<list>
    <ol>
        <li> Socket.io Servers & AWS </li>
        <li> Chat Filter System </li>
        <li> Background Hills Canvas drawing </li>
        <li> Clouds Background </li>
        <li> "ad" bug fix </li>
        <li> Instant death for Normal and Hard </li>
    </ol>
</list>

<h2> Socket.io Servers & AWS </h2>

<p> The Socket.io Servers allow multiplayer to be accessed to many players in the game. In this code, we need to define the properties of the socket as javascript isn't gonna recognize them in its database. Then we can put these codes in an io server, by which it'll allow many people to access a server at a time. This server is included with AWS, by which you have to pay for or connect to.</p>

```
export class Socket{
/**
 * @property {boolean} shouldBeSynced - should we be connected to the server
 * @property {Object} socket - used by Multiplayer, creates socket for the client
 * @property {string} socketID - id given by the websocket server when a player connects
 */

    static shouldBeSynced = true;
    //static socket = io("wss://platformer.nighthawkcodingsociety.com"); //aws server
    static socket = io(`ws://${window.location.host.split(":")[0]}:3000`); //local server
    static socketId;
    static {
        this.socket.on("id",(id)=>{this.socketId = id});
    }

    constructor(){throw new Error('Socket is a static class and cannot be instantiated.');}

    static sendData(key,value) {
        if (this.shouldBeSynced == false){return "offline"};
        if (typeof key != "string"){return "key is not a string"};
        this.socket.emit(key,value);
    }

    static createListener(key, func){
        if (this.shouldBeSynced == false){return "offline"};
        if (typeof key != "string"){return "key is not a string"};
        this.socket.on(key,func);
    }

    static removeListener(key){
        if (typeof key == "string"){return "key is not a string"};
        this.socket.off(key)
    }

    static removeAllListeners(){
        this.socket.removeAllListeners();
    }

    static changeStatus(){
        this.shouldBeSynced = !this.shouldBeSynced;
        if(this.shouldBeSynced){
            this.removeAllListeners();

            GameControl.transitionToLevel(GameEnv.levels[GameEnv.levels.indexOf(GameEnv.currentLevel)]);
        } else{
            GameControl.transitionToLevel(GameEnv.levels[GameEnv.levels.indexOf(GameEnv.currentLevel)]);
        }
        return this.shouldBeSynced;
    }
}
export default Socket;
```

<h2> Chat Filter System </h2>

<p> The chat filter system is a system where if anybody says a restricted word, then the word would be censored out. In this case, we need to add a constructor class to make the array inside the constructor class defined, so that it cna count every single word in that array.</p>

```
//Put this in chat.js:
//Constructor class that will be used
constructor(wordsToAdd){
        this.prohibitedWords = ['westview', 'pee', 'poo', 
        'multiplayer', 'multi', 'leaderboard', 'enemies', 
        'gamelevels', 'interactions', 'sass', 'sassy', 'sas', 
        '911', 'die', 'luigi', 'peach', 'bowser', 'mario', 
        'mr.mortensen', 'mr. mortensen', 'mortensen', 'lopez', 
        'mr.lopez', 'mr. lopez','mister mortensen', 'mister lopez', 
        'aws', 'amazonwebservices', 'amazon', 'amazonweb'];

        this.prohibitedWords.concat(wordsToAdd);
    }

    soundSource = "/game_levels_mp/assets/audio/discord-ping.mp3";
    soundArray = [];

    sendMessage(message){
        message = this.parseMessage(message);  
        Multiplayer.sendData("message",message);
    }

//forEach refers to EVERY word, meaning if you don't want it to censor all words, just delete that version
//This code replaces every word in this case with "I LOVE CSSE!"
//then the message is returned early.
    parseMessage(message){
        this.prohibitedWords.forEach(word => {
            const regex = new RegExp('\\b' + word + '\\b', 'gi');
            message = message.replace(regex, 'I Love CSSE! '.repeat(word.length));
        });
        return message;
    }
```

<p> the background hills canvas drawing puts the hills background, duplicates the background on both the left and right side, and created into separate parts so when the character goes to the left or right, the background will go the opposite way that the player is going. To ensure that the player doesn't cut out the background, this is the code that I added </p>

```
draw() {
        // Draw the primary segment
        this.ctx.drawImage(this.image, this.x, this.y);
        
        // Draw the wrap-around segment for the left side
        this.ctx.drawImage(this.image, this.x - this.width, this.y);
    
        // Draw the wrap-around segment for the right side
        this.ctx.drawImage(this.image, this.x + this.width, this.y);
    }

```
<p> this code ensures that wherever the character goes, the backgrounds on the left and right will follow it as well, and if the base image for hills is extended, then it prints wherever the player on the opposite side is </p>

<h2> Clouds BG </h2>

<p> The clouds background is similar to the way the hills background was made, however the speed of the BG is gonna be different and the overlay is gonna be behind the hills bg instead of infront. The 1st set of code is similar to the hills background, that which based on the variables from game environment, it'll update the speed of the requested clouds background and also draw the segments out. </p>

```
//Create a new file and name it BackgroundClouds.js
import GameEnv from './GameEnv.js';
import Background from './Background.js';

export class BackgroundClouds extends Background {
    constructor(canvas, image, data) {
        super(canvas, image, data);
    }

    // speed is used to background parallax behavior
    update() {
        this.speed = GameEnv.backgroundCloudsSpeed;
        super.update();
    }

    draw() {
        this.ctx.clearRect(0, 0, this.canvas.width, this.canvas.height);
        super.draw();
    }

}

export default BackgroundClouds;
```
<p> now because we made a new javascript class, if we want to add variables into other javascript classes, we need to import it into it so it'll be able to accept it & be able to define it.</p>

```
//Put it in ALL javascript files that we're updating:
import BackgroundClouds from './BackgroundClouds.js';

```
<p> Because most of these properties are in Game Environment, we have to update the code with the properties and also add the variable "BackgroundClouds" into a static so it'll be recognized by GameEnv </p>

```
//Under GameEnv.js
     export class GameEnv {

    /**
     * @static
     * @property {string} userID - localstorage key, used by GameControl
     * @property {Object} player - used by GameControl
     * @property {Array} levels - used by GameControl
     * @property {Object} currentLevel - used by GameControl
     * @property {Array} gameObjects - used by GameControl
     * @property {boolean} isInverted - localstorage key, canvas filter property, used by GameControl
     * @property {boolean} invincible - invincibility for the mario when stomping on Goomba
     * @property {number} gameSpeed - localstorage key, used by platformer objects
     * @property {number} backgroundHillsSpeed - used by background objects
     * @property {number} backgroundMountainsSpeed - used by background objects
     * @property {number} backgroundCloudsSpeed - used by background objects //Add this code!!
     * @property {boolean} transitionHide - used to hide the transition screen
     * @property {number} gravity - localstorage key, used by platformer objects
     * @property {Object} difficulty - localstorage key, used by GameControl
     * @property {number} innerWidth - used by platformer objects
     * @property {number} prevInnerWidth - used by platformer objects
     * @property {number} innerHeight - used by platformer objects
     * @property {number} top - used by platformer objects
     * @property {number} bottom - used by platformer objects
     * @property {number} prevBottom - used by platformer objects
     * @property {number} time - Initialize time variable, used by timer objects
     * @property {number} timerInterval - Variable to hold the interval reference, used by timer objects
     */
    static userID = "Guest";
    static player = null;
    static levels = [];
    static currentLevel = null;
    static gameObjects = [];
    static isInverted = false;
    static gameSpeed = 2;
    static backgroundHillsSpeed = 0;
    static backgroundMountainsSpeed = 0;
    static backgroundCloudsSpeed = 2; //Add this code !!!
    static transitionHide = false;
    static gravity = 3;
    static difficulty = "normal";
    static innerWidth;
    static prevInnerWidth;
    static innerHeight;
    static top;
    static bottom;
    static prevBottom;
    static invincible = false;
```

<p> Now that the speed and Game Environment recognizes the variable, we can add it to the main game. We'll put it in GameSetup.js. First we'll focus on the Game setup, which is how styles are set up and how levels begin or end. Under assets/backgrounds in GameSetup.js, lets put this code down in order for the image to be recognized:</p>

```
backgrounds: {
        start: { src: "/images/platformer/backgrounds/home.png" },
        hills: { src: "/images/platformer/backgrounds/hills.png" },
        avenida: { src: "/images/platformer/backgrounds/avenidawide3.jpg" },
        mountains: { src: "/images/platformer/backgrounds/mountains.jpg" },
        clouds : { src: "/images/platformer/backgrounds/clouds.png"}, //Make sure we put in this one
        space: { src: "/images/platformer/backgrounds/planet.jpg" },
        castles: { src: "/images/platformer/backgrounds/castles.png" },
        loading: { src: "/images/platformer/backgrounds/greenscreen.png" },
        complete: { src: "/images/platformer/backgrounds/OneStar.png" },
        complete2: { src: "/images/platformer/backgrounds/TwoStar.png" },
        end: { src: "/images/platformer/backgrounds/Congratulations!!!.png" }
      },
```

<p>This one depends on where you want to put the clouds in. Because it works better with the first level, we're gonna put it in hills. Lets go to const "HillsGameObject"</p>

```
const hillsGameObjects = [
        // GameObject(s), the order is important to z-index...
        { name: 'mountains', id: 'background', class: BackgroundMountains,  data: this.assets.backgrounds.mountains },
        // vvvvv Put in this one below vvvvv
        { name: 'clouds', id: 'background', class: BackgroundClouds, data: this.assets.backgrounds.clouds },
        { name: 'hills', id: 'background', class: BackgroundHills, data: this.assets.backgrounds.hills },
        { name: 'grass', id: 'platform', class: Platform, data: this.assets.platforms.grass },
        { name: 'blocks', id: 'jumpPlatform', class: BlockPlatform, data: this.assets.platforms.block, xPercentage: 0.2, yPercentage: 0.85 },
        { name: 'blocks', id: 'jumpPlatform', class: BlockPlatform, data: this.assets.platforms.block, xPercentage: 0.2368, yPercentage: 0.85 },
        { name: 'blocks', id: 'jumpPlatform', class: BlockPlatform, data: this.assets.platforms.block, xPercentage: 0.2736, yPercentage: 0.85 },
        { name: 'blocks', id: 'jumpPlatform', class: BlockPlatform, data: this.assets.platforms.block, xPercentage: 0.6, yPercentage: 1 },
        { name: 'itemBlock', id: 'jumpPlatform', class: JumpPlatform, data: this.assets.platforms.itemBlock, xPercentage: 0.4, yPercentage: 0.65 }, //item block is a platform
        { name: 'goomba', id: 'goomba', class: Goomba, data: this.assets.enemies.goomba, xPercentage: 0.3, yPercentage: 1, minPosition: 0.05},
        { name: 'goomba', id: 'goomba', class: Goomba, data: this.assets.enemies.goomba, xPercentage:  0.5, yPercentage: 1, minPosition: 0.3 },
        { name: 'goombaSpecial', id: 'goomba', class: Goomba, data: this.assets.enemies.goomba, xPercentage:  0.75, yPercentage: 1, minPosition: 0.5 }, //this special name is used for random event 2 to make sure that only one of the Goombas ends the random event
        { name: 'mario', id: 'player', class: Player, data: this.assets.players.mario },
        { name: 'tube', id: 'tube', class: Tube, data: this.assets.obstacles.tube },
        { name: 'loading', id: 'background', class: BackgroundTransitions,  data: this.assets.backgrounds.loading },
        ];
```

<h2> "ad" fix </h2>

<p> "ad" fix is where when you hold both "a" and "d" at the same time, mario would stay in place. Because this can probably mess up the background and stuff, we want a control to override the other one. Under player.js, in handleKeyDown(event) this is the code that we put in. </p>

```
    handleKeyDown(event) {
        if (this.playerData.hasOwnProperty(event.key)) {
            const key = event.key;
            if (!(event.key in this.pressedKeys)) {
                //If both 'a' and 'd' are pressed, then only 'd' will be inputted
                //Originally if this is deleted, player would stand still. 
                if (this.pressedKeys['a'] && key === 'd') {
                    delete this.pressedKeys['a']; // Remove "a" key from pressedKeys
                    return; //(return) = exit early
                } else if (this.pressedKeys['d'] && key === 'a') {
                    // If "d" is pressed and "a" is pressed afterward, ignore "a" key
                    return;
                }
                this.pressedKeys[event.key] = this.playerData[key];
                this.setAnimation(key);
                // player active
                this.isIdle = false;
                GameEnv.transitionHide = true;
            }
        }
    }
```

<p> in this code, if both "a" and "d" are pressed, it'll run an if statement, by which if the conditions are true, the key "a" will be deleted and "d" will override the key up until the player lets go of either key, which is what the return function does. </p>

<h2> Instant Death System </h2>

<p> When a player collides with a gomba, most of the time they respawn. However on difficulties such as normal or hard, they need to have a twist in order to make stuff more difficult for the player & have more gameplay for them. To do this, we go into player.js because we need to know when the player dies in order to recognize if the instant death system is in play. </p>

```
 // Instant Death System (For Harder Stages besides impossible)
                if (GameEnv.difficulty === "normal" || GameEnv.difficulty === "hard") {
                    if (this.isDying == false) {
                        this.isDying = true;
                        setTimeout(async() => {
                            await GameControl.transitionToLevel(GameEnv.levels[GameEnv.levels.indexOf(GameSetup.gameOverCallBack)]);
                            console.log("player died");
                            this.isDying = false;
                        }, 700)
                    }
                }
```

<p> In this code, it'll run whether or not the player dies in normal or hard mode. If the player is not in hard or normal mode, the code with be ignored, however if they are, it'll call for "gameOverCallBack" which ends the entire game and makes the screen go blank. Then right after the player dies, the death system will go back to false. </p>