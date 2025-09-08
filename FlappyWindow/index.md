<head>
  <title>FlappyWindow</title>
</head>

### [Back](..)
# *FlappyWindow*

Worked as **Programmer**    
*2025 August, 18 - 22*   
Language: c++    

---

I wanted to do something like this for a little while, I tried before in monogame but that didnt allow you to have multiple windows moving and running code at the same time. So I gave up after a while.

When we started working in C++ and SDL3 and I saw that you could easily make windows so I thought that maybe I could revive this idea. So I started experimenting by making the bird and spawning windows. When it worked I got exited and continued working. I added sprites, animated the bird, and scoring.

Something interesting that happend with the score text, When I wrote this line to change the window title:
```
SDL_SetWindowTitle(scoreWindow, "Score: " + score);
```
The text window took away letter by letter from the start and eventually started writing out error messages and other random things that it still removed letter by letter. What I think happend is that it set the text to the string I wrote but start at letter number <code>score</code>, eventually me and my classmates solved it by writing this instead:
```
SDL_SetWindowTitle(scoreWindow, std::format("Score: {}", score).c_str());
```
It formats the text and then converts the formatted string into characters since thats what the function needs.

<details>
<summary>Bird.cs</summary>
<pre>
<code>
void Bird::Tick()
{
	switch (game-&gt;gameState)
	{
	case Game::GameState::Start:
		Start();
		break;
	case Game::GameState::Playing:
		Playing();
		break;
	case Game::GameState::Dead:
		Dead();
		break;
	}
}

void Bird::Start()
{
	position-&gt;y = 0;
	velocity = 0;
	const bool* keys = SDL_GetKeyboardState(NULL);
	SDL_PumpEvents();
	if (keys[SDL_SCANCODE_SPACE])
	{
		game-&gt;SetGameState(Game::GameState::Playing);
		std::cout &lt;&lt; "Play" &lt;&lt; '\n';
	}

	CoordinateConverter::SetWindowWorldPositionCentered(window, *(position));
}

void Bird::Playing()
{
	const bool* keys = SDL_GetKeyboardState(NULL);
	SDL_PumpEvents();
	velocity += gravity * game-&gt;fdata.deltaTime;
	if (keys[SDL_SCANCODE_SPACE] && !flapped) {
		if (velocity &lt; 0)
		{
			velocity = flapPower;
		}
		else
		{
			velocity += flapPower * 0.75;
		}
		if (velocity &gt; maxVelocity) { velocity = maxVelocity; }
		flapped = true;
	}
	if (!keys[SDL_SCANCODE_SPACE]) { flapped = false; }

	if (position-&gt;y &gt; CoordinateConverter::worldHeight/2) //Ceiling handling
	{
 		velocity = 0;
		position-&gt;y = CoordinateConverter::worldHeight / 2;
	}
	else if (position-&gt;y &lt; -CoordinateConverter::worldHeight / 2) //Floor handling
	{
		game-&gt;SetGameState(Game::GameState::Dead);
	}

	position-&gt;y += velocity * game-&gt;fdata.deltaTime;
	CoordinateConverter::SetWindowWorldPositionCentered(window, *(position));
}

void Bird::Dead()
{
	const bool* keys = SDL_GetKeyboardState(NULL);
	SDL_PumpEvents();
	if (keys[SDL_SCANCODE_SPACE])
	{
		game-&gt;SetGameState(Game::GameState::Start);
		std::cout &lt;&lt; "Restart" &lt;&lt; '\n';
	}
}

Bird::Bird(Game* game)
{
	this-&gt;game = game;
	window = SDL_CreateWindow("Bird", 100, 100, 0);
}
</code>
</pre>
</details>

## Screenshots

<table>
  <tr>
    <td><img src="Images\Gameplay.gif" /></td>
  </tr>
</table>
