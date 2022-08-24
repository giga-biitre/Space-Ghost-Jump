import kaboom from "kaboom";

//const BACKGROUND = 700;
const FLOOR_HEIGHT = 48;
const JUMP_FORCE = 570;
var SPEED = 460;
var GRAVITY2 = 1950;

// initialize context
kaboom();

// load assets
loadSprite("bean", "sprites/bean.png");
loadSprite ("back", "sprites/back.jpeg");

loadSound("OtherworldlyFoe", "sounds/OtherworldlyFoe.mp3");
const music = play("OtherworldlyFoe", {
    volume: 0.8,
    loop: true
})

scene("game", () => {

add([
	sprite("back", { width: width(), height: height()})
])

	

	// add a game object to screen
	const player = add([
		// list of components
		sprite("bean"),
		pos(20, 0),
		area(),
		body(),
	]);

	
	add([
		text("Press space to jump"),
		pos(width() / 2, height() / 2-80),
		scale(0.5),
		origin("center"),
	])

	// background
	//add([
		//rect(width(), BACKGROUND),
		//pos(0, height()),
		//origin("botleft"),
		//area(),
		//color(30, 49, 120),
	//]);

	// floor
	add([
		rect(width(), FLOOR_HEIGHT),
		outline(4),
		pos(0, height()),
		origin("botleft"),
		area(),
		solid(),
		color(168, 176, 189),
	]);

	function jump() {
		if (player.grounded()) {
			player.jump(JUMP_FORCE);
			SPEED = SPEED + 15;
			//GRAVITY2 = GRAVITY2 + 50;
		}
	}
	
	keyPress("space", jump); //computer players
	mouseClick(jump);  //mobile players
	

	function spawnTree() {
		// add tree 
		add([
			rect(48, rand(30, 70)),
			area(),
			outline(4),
			pos(width(), height() - FLOOR_HEIGHT),
			origin("botleft"),
			color(128, 138, 156),
			move(LEFT, SPEED),
			"tree",
		]);

// wait a random amount of time to spawn next tree
		wait(rand(0.7, 2.0), spawnTree);
	}

	spawnTree();

	player.collides("tree", () => {
		// go to "lose" scene and pass the score
		go("lose", score);
		burp();
		addKaboom(player.pos);
	});

	// keep track of score
	let score = 0;

	const scoreLabel = add([
		text(score),
		pos(24, 24),
	]);

	// increment score every frame
	action(() => {
		score++;
		scoreLabel.text = score;
	});

});

scene("lose", (score) => {

	add([
		sprite("bean"),
		pos(width() / 2, height() / 2 - 80),
		scale(2),
		origin("center"),
	]);

	add([
		text("Oh no! You Lost!"),
		pos(width() / 2, height() / 2 + 170),
		scale(1),
		origin("center"),
	])

	// display score
	add([
		text(score),
		pos(width() / 2, height() / 2 + 80),
		scale(2),
		origin("center"),
	]);

	// go back to game with space is pressed
	keyPress("space", () => go("game"));
	mouseClick(() => go("game"));

});

go("game");
