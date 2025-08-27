### [Back](..)
# *Water Drop Racing*

Worked as **Programmer**    
*2025 August, 18 - 22*   

---

I wanted to do something like this for a little while, I tried before in monogame but that didnt allow you to have multiple windows moving and running code at the same time. So I gave up after a while.

When we started working in C++ and SDL3 and I saw that you could easily make windows so I thought that maybe I could revive this idea. So I started experimenting by making the bird and spawning windows. When it worked I got exited and continued working. I added sprites, animated the bird, and scoring.

Something interesting that happend with the score text, When I wrote this line to change the window title:
```c++
SDL_SetWindowTitle(scoreWindow, "Score: " + score);
```
The text window took away letter by letter from the start and eventually started writing out error messages and other random things that it still removed letter by letter. What I think happend is that it set the text to the string I wrote but start at letter number ``score``, eventually me and my classmates solved it by writing this instead:
```c++
SDL_SetWindowTitle(scoreWindow, std::format("Score: {}", score).c_str());
```
It formats the text and then converts the formatted string into characters since thats what the function needs.

## Screenshots

<table>
  <tr>
    <td><img src="Images\Gameplay.png" /></td>
  </tr>
</table>
