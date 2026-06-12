# Code

```
// Asad Khan

import java.io.*; 
import java.util.Scanner; 
import javax.swing.JOptionPane; 

public class AuthSystem 
{
    
    private static final String AUTH_FILE = "accounts.txt";
    
    public static boolean registerUser(String username, String password)
    {
        try 
        {
            File file = new File(AUTH_FILE);
            
            // $Asad$ Check if the file exists to look for duplicate usernames
            if (file.exists()) 
            {
                Scanner scanner = new Scanner(file);
                while (scanner.hasNextLine()) 
                {
                    // $Asad$ Split the line at the colon, parts[0] is username, parts[1] is password
                    String[] parts = scanner.nextLine().split(":");
                    
                    // $Asad$ If the username already exists (ignoring the capitalization), stop registration
                    if (parts[0].equalsIgnoreCase(username)) 
                    {
                        scanner.close();
                        return false; 
                    }
                }
                scanner.close();
            }
            
            // $Asad$ If username is unique, open the file to add the new username and password without deleting previous
            // $Asad$ This adds the new user to the end of the file without deleting existing users
            FileWriter writer = new FileWriter(AUTH_FILE, true);
            writer.write(username + ":" + password + System.lineSeparator());
            writer.close();
            return true; 
            
        } 
        
        catch (IOException e) 
        {
            return false; // Returns false if a file error happens
        }
    }
    

    // $Asad$ Checks if the typed username and password matches records in file
    public static boolean loginUser(String username, String password) 
    {
        try 
        {
            File file = new File(AUTH_FILE);
            // $Asad$ If no accounts file exists yet, nobody can log in
            if (!file.exists())
            {
                return false;
            }
            Scanner scanner = new Scanner(file);
            // $Asad$ Read the file line by line to look for a match
            while (scanner.hasNextLine()) 
            {
                String[] parts = scanner.nextLine().split(":");
                
                // $Asad$ Check if both the username and password match 
                if (parts[0].equalsIgnoreCase(username) && parts[1].equals(password)) 
                {
                    scanner.close();
                    return true; // $Asad$ Match found to login
                }
            }
            scanner.close(); // $Asad$ Close scanner if loop finishes with no match
        } 
        catch (IOException e) 
        {
            return false;
        }
        return false; // $Asad$ Return false if username/password wasn't found
    }
}

---

import java.awt.*;

public class Fish extends GameObject 
{

    public Fish(int x, int y, int width, int height, int speed) 
    {
        super(x, y, width, height, speed);
    }

    @Override
    public void draw(Graphics2D g2d) 
    {
        // Drawing a Hostile Red Fish)
        g2d.setColor(new Color(231, 76, 60));
        g2d.fillOval(x, y, width, height);
        
        // Tail Fin
        int[] xPoints = {x + width, x + width + 15, x + width + 15};
        int[] yPoints = {y + height / 2, y, y + height};
        g2d.fillPolygon(xPoints, yPoints, 3);
        
        // Angered Eye
        g2d.setColor(Color.BLACK);
        g2d.fillOval(x + 8, y + 8, 5, 5);
    }
}

---

import java.awt.*;

public abstract class GameObject 
{
    protected int x, y, width, height, speed;

    public GameObject(int x, int y, int width, int height, int speed) 
    {
        this.x = x;
        this.y = y;
        this.width = width;
        this.height = height;
        this.speed = speed;
    }

    public void update() 
    {
        x -= speed; // Move items from right to left
    }

    public abstract void draw(Graphics2D g2d);
    
    public Rectangle getBounds() 
    {
        return new Rectangle(x, y, width, height);
    }

    public int getX() 
    { 
        return x; 
    }
}

---

import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import java.util.ArrayList;
import java.util.Iterator;
import java.util.Random;

public class GamePanel extends JPanel implements ActionListener, KeyListener {
    // Screen Dimensions
    private static final int WIDTH = 800;
    private static final int HEIGHT = 600;

    // Game Loop Timer
    private Timer timer;
    private boolean gameOver = false;
    private int score = 0;
    private int health = 3;
    private int highRecord = 0;

    // Submarine Properties
    private int subX = 100;
    private int subY = 250;
    private final int subWidth = 70;
    private final int subHeight = 40;
    private int subSpeed = 7;

    // Input Tracking
    private boolean upPressed = false;
    private boolean downPressed = false;
    private boolean leftPressed = false;
    private boolean rightPressed = false;

    // Game Objects
    private ArrayList<GameObject> entities = new ArrayList<>();
    private Random random = new Random();
    private int spawnCounter = 0;

    public GamePanel() {
        setPreferredSize(new Dimension(WIDTH, HEIGHT));
        setBackground(new Color(12, 45, 72)); // Deep ocean blue
        setFocusable(true);
        addKeyListener(this);

        // Game loop ticks every 16ms (~60 FPS)
        timer = new Timer(16, this);
        this.highRecord = ScoreSystem.getGlobalHighScore();
        timer.start();
    }

    @Override
    protected void paintComponent(Graphics g) {
        super.paintComponent(g);
        Graphics2D g2d = (Graphics2D) g;
        g2d.setRenderingHint(RenderingHints.KEY_ANTIALIASING, RenderingHints.VALUE_ANTIALIAS_ON);

        // Draw Background Details (Water particles/bubbles)
        g2d.setColor(new Color(255, 255, 255, 30));
        for (int i = 0; i < 10; i++) {
            g2d.fillOval((i * 90 + score) % WIDTH, (i * 70) % HEIGHT, 8, 8);
        }

        // Draw Entities (Creatures and Treasures)
        for (GameObject entity : entities) {
            entity.draw(g2d);
        }

        // Draw Submarine
        drawSubmarine(g2d, subX, subY);

        // Draw UI (Score & Health)
        g2d.setColor(Color.WHITE);
        g2d.setFont(new Font("Arial", Font.BOLD, 18));
        g2d.drawString("TREASURE: $" + score, 20, 30);
        g2d.drawString("Global High Score: $" + highRecord, 20, 55);
        g2d.drawString("Lives: " + health + "/3", 650, 30);
        
        // Game Over Screen
        if (gameOver) {
            g2d.setColor(new Color(0, 0, 0, 180));
            g2d.fillRect(0, 0, WIDTH, HEIGHT);

            g2d.setColor(Color.RED);
            g2d.setFont(new Font("Arial", Font.BOLD, 50));
            g2d.drawString("GAME OVER", WIDTH / 2 - 150, HEIGHT / 2 - 20);

            g2d.setColor(Color.WHITE);
            g2d.setFont(new Font("Arial", Font.PLAIN, 20));
            g2d.drawString("You collected $" + score + " in treasure.", WIDTH / 2 - 130, HEIGHT / 2 + 20);
            g2d.drawString("Press 'R' to Restart", WIDTH / 2 - 60, HEIGHT / 2 + 60);
        }
    }

    private void drawSubmarine(Graphics2D g2d, int x, int y) {
        // Main Body
        g2d.setColor(new Color(241, 196, 15)); // Yellow sub
        g2d.fillOval(x, y, subWidth, subHeight);

        // Periscope
        g2d.setColor(new Color(212, 172, 13));
        g2d.fillRect(x + subWidth / 2, y - 15, 6, 15);
        g2d.fillRect(x + subWidth / 2, y - 15, 12, 6);

        // Propeller
        g2d.setColor(Color.LIGHT_GRAY);
        g2d.fillRect(x - 8, y + subHeight / 3, 8, subHeight / 3);

        // Windows
        g2d.setColor(new Color(41, 128, 185));
        g2d.fillOval(x + 15, y + 10, 12, 12);
        g2d.fillOval(x + 40, y + 10, 12, 12);
    }

    @Override
    public void actionPerformed(ActionEvent e) {
        if (!gameOver) {
            updateMovement();
            handleSpawning();
            updateEntities();
            checkCollisions();
        }
        repaint();
    }

    private void updateMovement() {
        if (upPressed && subY > 0) subY -= subSpeed;
        if (downPressed && subY < HEIGHT - subHeight) subY += subSpeed;
        if (leftPressed && subX > 0) subX -= subSpeed;
        if (rightPressed && subX < WIDTH - subWidth) subX += subSpeed;
    }

    private void handleSpawning() {
        spawnCounter++;
        if (spawnCounter % 35 == 0) {
            int spawnY = random.nextInt(HEIGHT - 50) + 10;
            if (random.nextBoolean()) {
                entities.add(new Fish(WIDTH, spawnY, 40, 30, random.nextInt(3) + 4));
            } else {
                entities.add(new Treasure(WIDTH, spawnY, 25, 25, 4));
            }
        }
    }

    private void updateEntities() {
        Iterator<GameObject> iterator = entities.iterator();
        while (iterator.hasNext()) {
            GameObject entity = iterator.next();
            entity.update();
            if (entity.getX() + 40 < 0) {
                iterator.remove();
            }
        }
    }

    private void checkCollisions() {
        Rectangle subBounds = new Rectangle(subX, subY, subWidth, subHeight);
        Iterator<GameObject> iterator = entities.iterator();

        while (iterator.hasNext()) {
            GameObject entity = iterator.next();

            if (subBounds.intersects(entity.getBounds())) { 
                if (entity instanceof Fish) {
                    ScoreSystem.updateHighScore(health);
                    this.highRecord = ScoreSystem.getGlobalHighScore();
                    health--;
                    if (health <= 0) {
                        gameOver = true;
                        timer.stop();
                        ScoreSystem.updateHighScore(score);
                    }
                } else if (entity instanceof Treasure) {
                    score += 100;
                }
                iterator.remove();
            }
        }
    }

    @Override
    public void keyPressed(KeyEvent e) {
        int code = e.getKeyCode();
        if (code == KeyEvent.VK_UP || code == KeyEvent.VK_W) upPressed = true;
        if (code == KeyEvent.VK_DOWN || code == KeyEvent.VK_S) downPressed = true;

        if (gameOver && code == KeyEvent.VK_R) {
            restartGame();
        }
    }

    @Override
    public void keyReleased(KeyEvent e) {
        int code = e.getKeyCode();
        if (code == KeyEvent.VK_UP || code == KeyEvent.VK_W) upPressed = false;
        if (code == KeyEvent.VK_DOWN || code == KeyEvent.VK_S) downPressed = false;
    }

    @Override
    public void keyTyped(KeyEvent e) {}

    private void restartGame() {
        subX = 100;
        subY = 250;
        score = 0;
        health = 3;
        entities.clear();
        gameOver = false;
        
        highRecord = ScoreSystem.getGlobalHighScore(); 
    
        timer.start();
    }
}

---

import java.io.*;
import java.util.Scanner;

public class ScoreSystem 
{
    // $Asad$ The name of the file the single highest score value
    private static final String SCORE_FILE = "highscore.txt";

    // $Asad$ Reads and returns the top score currently saved on the machine
    public static int getGlobalHighScore() 
    {
        try 
        {
            File file = new File(SCORE_FILE);
            // $Asad$ If the file doesn't exist yet, the record is 0
            if (!file.exists())
            {
                return 0;
            }
            // $Asad$ Open scanner to read the integer stored in the file
            Scanner scanner = new Scanner(file);
            if (scanner.hasNextInt())
            {
                int high = scanner.nextInt();
                scanner.close();
                return high;
            }
            scanner.close();
            return 0;
        } 
        catch (Exception e) 
        {
            return 0; // $Asad$ Goes to 0 if the file is corrupted or empty
        }
    }

    // $Asad$ Compares the game's current score to the all-time record and updates it if beaten
    public static void updateHighScore(int currentScore) 
    {
        // $Asad$ Only run if the player actually beat the previous high score
        if (currentScore > getGlobalHighScore()) 
        {
            try 
            {
                // $Asad$ This clears out the old number and replaces it with the brand new record
                FileWriter writer = new FileWriter(SCORE_FILE);
                writer.write(String.valueOf(currentScore));
                writer.close();
            } 
            catch (IOException e) 
            {
                System.out.println("Error saving high score.");
            }
        }
    }
}

---

import javax.swing.*;
import javax.swing.JOptionPane;

public class SubmarineGame extends JFrame {
    public SubmarineGame() {
        
        String user = "Guest"; 
        
        String[] options = {"Login", "Register", "Guest Mode"};
        
        // $Asad$ Open a popup box with the 3 buttons. 
        // $Asad$ It returns a number based on what was clicked: 0 = Login, 1 = Register, 2 = Guest Mode
        int choice = JOptionPane.showOptionDialog(null, "Welcome to Trench Diver! Choose an option:", "Authentication", JOptionPane.DEFAULT_OPTION, JOptionPane.QUESTION_MESSAGE, null, options, options[0]
        );
        
        if (choice == 0 || choice == 1) 
        {
            // $Asad$ Open an input box asking for their username
            String username = JOptionPane.showInputDialog("Enter Username:");
            // Open a second input box asking for their password
            String password = JOptionPane.showInputDialog("Enter Password:");
        
            // $Asad$ Check if they clicked Cancel, left it blank, or just typed spaces
            if (username != null && password != null && !username.trim().isEmpty() && !password.trim().isEmpty()) 
            {
                // $Asad$ To strip off any accidental spaces typed at the beginning or end of the username
                username = username.trim();
                
                // $Asad$ If they originally clicked the "Login" button
                if (choice == 0) 
                { 
                    // $Asad$ Send the username and password to your AuthSystem file to check for a match
                    if (AuthSystem.loginUser(username, password)) 
                    {
                        // $Asad$ If a match is found, change the active user from Guestdefault to their username
                        user = username;
                        JOptionPane.showMessageDialog(null, "Login Successful! Welcome back, " + user);
                    } 
                    else 
                    {
                        // $Asad$ If no match is found, tell them and keep them as a Guest
                        JOptionPane.showMessageDialog(null, "Invalid login. Running as Guest.");
                    }
                } 
                // $Asad$ If they didn't click Login, they must have clicked the "Register" button
                else 
                { 
                    // $Asad$ Send the new info to AuthSystem to see if the username is available and save it
                    if (AuthSystem.registerUser(username, password)) {
                        // $Asad$ log them in automatically under the new name
                        user = username;
                        JOptionPane.showMessageDialog(null, "Registration Successful! Account created.");
                    } 
                    else 
                    {
                        // $Asad$ If registerUser returns false, it means that username is already in accounts.txt
                        JOptionPane.showMessageDialog(null, "Username taken. Running as Guest.");
                    }
                }
            }
        } 
        // $Asad$ they clicked "Guest Mode" (2) or closed out the popup window entirely
        else 
        {
            // $Asad$ Skip everything and just launch the game directly as a Guest
            JOptionPane.showMessageDialog(null, "Starting game in Guest Mode.");
        }
        
        setTitle("Trench Diver: Submarine Adventure");
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setResizable(false);
        
        // Add the game panel
        GamePanel gamePanel = new GamePanel();
        add(gamePanel);
        pack();
        
        setLocationRelativeTo(null); // Center on screen
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(() -> {
            new SubmarineGame().setVisible(true);
        });
    }
}

---

import java.awt.*;

public class Treasure extends GameObject 
{

    public Treasure(int x, int y, int width, int height, int speed) 
    {
        super(x, y, width, height, speed);
    }

    @Override
    public void draw(Graphics2D g2d) 
    {
        // Drawing the Golden Chest
        g2d.setColor(new Color(243, 156, 18));
        g2d.fillRect(x, y, width, height);
        g2d.setColor(new Color(241, 196, 15));
        g2d.fillRect(x + 2, y + 2, width - 4, height - 4);
        
        // Lock
        g2d.setColor(Color.DARK_GRAY);
        g2d.fillRect(x + width / 2 - 3, y + height / 2, 6, 6);
    }
}
