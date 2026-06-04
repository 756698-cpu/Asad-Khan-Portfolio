# Trench Diver - Java Swing Maritime Survival Game

## Project Overview & Purpose
**Trench Diver** is an arcade-style, horizontal side-scroller. The player commands a deep-sea submarine navigating through a treacherous underwater cavern. As the submarine travels deeper into the trench, the player must use rapid reflexes to maneuver around hostile sea creatures and floating debris while collecting sunken treasures to maximize their score.

## Core Objectives:
1. **Survive:** Dodge randomly spawning sea obstacles that scroll from right to left.
2. **Collect:** Gather rare treasures to increase your score.
3. **Compete:** Save your final score to a persistent local leaderboard.

## 🛠️ Computer Science Concepts Applied

This project translates core Object-Oriented Programming (OOP) concepts learned throughout the term into a functional application:

* **Inheritance:** Features a structure utilizing an abstract `SeaCreature` superclass, which branches into distinct subclasses (`Shark`, `Jellyfish`) with specialized movement behaviors.
* **File I/O:** Reads from and writes to a local `leaderboard.txt` file to persist user high scores across game sessions.
* **Animation & Game Loop:** Employs a Java Swing `Timer` running at ~60 FPS to handle physics updates and smooth component rendering.
* **Event Handling:** Uses a custom `KeyAdapter` to listen for keyboard inputs (`Up` / `Down` arrows) for seamless submarine navigation.
* **Swing Components:** Integrates a variety of `JComponents` including `JButton`, `JLabel`, and custom `JPanel` canvases for the HUD and main menu system.



## How to Run the Program

### Running on CodeHS
1. Create three separate files in your page named exactly:
   * `PongGame.java`
   * `AuthSystem.java`
   * `ScoreSystem.java`
2. Hit **Run Code**.

## References & Documentation
* Uses javax.swing.JOptionPane for the popups and entry boxes.

* Uses java.io.FileWriter and java.util.Scanner to handle saving and reading files.

## How to Play
* Left Paddle: Use W to move up, S to move down.
* Right Paddle: Use the Up Arrow to move up, Down Arrow to move down.
* Goal: Deflect the ball past the opponent's paddle. 






## 🛠️ Computer Science Concepts Applied

This project translates core Object-Oriented Programming (OOP) concepts learned throughout the term into a functional application:

* **Inheritance:** Features a structure utilizing an abstract `SeaCreature` superclass, which branches into distinct subclasses (`Shark`, `Jellyfish`) with specialized movement behaviors.
* **File I/O:** Reads from and writes to a local `leaderboard.txt` file to persist user high scores across game sessions.
* **Animation & Game Loop:** Employs a Java Swing `Timer` running at ~60 FPS to handle physics updates and smooth component rendering.
* **Event Handling:** Uses a custom `KeyAdapter` to listen for keyboard inputs (`Up` / `Down` arrows) for seamless submarine navigation.
* **Swing Components:** Integrates a variety of `JComponents` including `JButton`, `JLabel`, and custom `JPanel` canvases for the HUD and main menu system.

---

## 🚀 How To Run the Application

### Prerequisites
* Java Development Kit (JDK) 8 or higher installed.
* Git installed on your local machine.

### Execution Instructions
1. Clone the repository to your local machine:
   ```bash
   git clone [https://github.com/](https://github.com/)[YourGitHubUsername]/TrenchDiver.git
