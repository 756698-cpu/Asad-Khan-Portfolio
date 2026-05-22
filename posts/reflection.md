# Project Report: Upgrading the Pong Game Base

### What I Changed in the Code Structure
The original baseline code already had the basic object-oriented structure down with separate classes for `GameObject`, `Ball`, `Paddle`, and `PongPanel`. My editing focused on expanding these existing classes to support advanced features:
* **Modifying the Existing Classes:** I added frame counters directly into `Ball.java` for visual effects
* **Adding New Files:** I created `AuthSystem.java` and `ScoreSystem.java` as completely separate files so that the core gameplay code doesn't get mixed with file reading and writing logic.

This keeps the code organized and works smoothly.

---

### How the Requirements Were Met

#### Feature 1: Neon Collision Flash
I added a `flashFrames` counter directly to `Ball.java`. Inside the `updateLogic()` hit-detection loops in `PongPanel`, the code now triggers this flash whenever the ball bounces off a paddle. This forces the ball to paint itself bright white for 25 frames before turning back to normal, giving the player immediate visual feedback.

#### Feature 2: User Authentication (Login/Register Page)
I created a brand-new file called `AuthSystem.java`. It uses a `Scanner` and a `FileWriter` to check and save usernames and passwords in a text file called `accounts.txt`. On startup, I used a `JOptionPane` inside the `PongGame` constructor to create a popup menu. Players can register a new account, login to an existing one, or play instantly as a guest.

#### Feature 3: High Score Persistence (File I/O)
I created `ScoreSystem.java` to handle saving the high score to a file called `highscore.txt`. Every time a player scores a point in `updateLogic()`, the program checks if the new score beats the record. If it does, it overwrites the file with the new record. 
