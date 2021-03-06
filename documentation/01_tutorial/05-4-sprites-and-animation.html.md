```
title: "4 - Sprites and Animation"
```

So we have a player sprite that moves around the screen. Great! But… we don't want it to just look like a block… so let's add some graphics!

First, you need to have the sprite's graphic. Using your image editor of choice, you can draw pretty much anything you want, and save it as a transparent `.png`. For our player sprite, we're going to have it be made up of 16x16 pixel frames, with two different frames for each of the 4-possible facing directions (left and right are used by the same 2 frames, since we will be flipping them later).

You can draw it yourself, if you want, or use this image, created by my friend Vicky Hedgecock for this tutorial:

![](https://raw.githubusercontent.com/HaxeFlixel/flixel-demos/master/Tutorials/TurnBasedRPG/assets/images/player.png)

Save your file in `assets/images`.

Now we need to actually load the player's graphic into the sprite. So, bring up your `Player` class again.

1. Remove the `makeGraphic()` call from your constructor, and replace it with:

	```haxe
	loadGraphic(AssetPaths.player__png, true, 16, 16);
	```

	This tells your sprite to use the `player.png` graphic, that it's animated, and that each frame is 16x16 pixels. `AssetPaths` is a class generated by a neat [Haxe macro](http://haxe.org/manual/macros) which builds its variables from the contents of your `Project.xml`'s assets tag. Macros are a little complicated, but worth taking a look into at some point. In this case, we use `AssetsPaths` as an easy way to reference our assets in code.

2. Next, we want to allow the sprite to be flipped based on its `facing` value. This makes it so we only need sprites for one direction (left), and not two (left and right).

	Add the following:

	```haxe
	setFacingFlip(FlxObject.LEFT, false, false);
	setFacingFlip(FlxObject.RIGHT, true, false);
	```

	All we're doing here is saying that we don't want to flip anything when facing left (because our sprite already faces left), and to flip horizontally when facing right. We could do the same for up and down if we wanted.

3. Now, we need to define our animations. In our case, we want each animation to end with the player in their 'neutral' pose (legs together), that way each time we animate the player, it will return to the correct frame as soon as they stop animating. So, add:

	```haxe
	animation.add("lr", [3, 4, 3, 5], 6, false);
	animation.add("u", [6, 7, 6, 8], 6, false);
	animation.add("d", [0, 1, 0, 2], 6, false);
	```

	We're finished with the constructor changes, the final step is to change our `movement()` function to tell the player sprite which way to face. So, modify our section which deals with setting the player's angle to look something like this:

	```haxe
	var mA:Float = 0; // our temporary angle
	if (_up)  // the player is pressing UP
	{
		mA = -90; // set our angle to -90 (12 o'clock)
		if (_left)
			mA -= 45; // if the player is also pressing LEFT, subtract 45 degrees from our angle - we're moving up and left
		else if (_right)
			mA += 45; // similarly, if the player is pressing RIGHT, add 45 degrees (up and right)
		facing = FlxObject.UP; // the sprite should be facing UP
	}
	else if (_down) // the player is pressing DOWN
	{
		mA = 90; // set our angle to 90 (6 o'clock)
		if (_left)
			mA += 45; // add 45 degrees if the player is also pressing LEFT
		else if (_right)
			mA -= 45; // or subtract 45 if they are pressing RIGHT
		facing = FlxObject.DOWN; // the sprite is facing DOWN
	}
	else if (_left) // if the player is not pressing UP or DOWN, but they are pressing LEFT
	{
		mA = 180; // set our angle to 180 (9 o'clock)
		facing = FlxObject.LEFT; // the sprite should be facing LEFT
	}
	else if (_right) // the player is not pressing UP, DOWN, or LEFT, and they ARE pressing RIGHT
	{
		mA = 0; // set our angle to 0 (3 o'clock)
		facing = FlxObject.RIGHT; // set the sprite's facing to RIGHT
	}
	// determine our velocity based on angle and speed
	velocity.set(speed, 0);
	velocity.rotate(FlxPoint.weak(0, 0), mA);
	
	if ((velocity.x != 0 || velocity.y != 0) && touching == FlxObject.NONE) // if the player is moving (velocity is not 0 for either axis), we need to change the animation to match their facing
	{
		switch (facing)
		{
			case FlxObject.LEFT, FlxObject.RIGHT:
				animation.play("lr");
			case FlxObject.UP:
				animation.play("u");
			case FlxObject.DOWN:
				animation.play("d");
		}
	}
	```

	Every time this function gets called, it will check to see which of the directions the player is pressing, and, based on those, which way the sprite should be facing, and which animation should be playing..

4. Save your changes and run your project, and you should see your player sprite animate while being moved, and facing the right direction!

	![](../images/01_tutorial/0006b.png)

Next, we'll talk about maps and collision!
