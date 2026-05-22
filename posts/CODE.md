import java.io.*; // $Asad$
import java.util.Scanner; // $Asad$
import javax.swing.JOptionPane; // $Asad$

public class AuthSystem 
{
    // $Asad$ The name of the text file where user credentials will be saved
    private static final String AUTH_FILE = "accounts.txt";

    // $Asad$ Registers a new user. Returns true if successful, false if username is taken.
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
                    // $Asad$ Split the line at the colon -> parts[0] is username, parts[1] is password
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
            writer.write(username + ":" + password + "\n");
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

public class Ball extends GameObject {
    private double velX = 4.0;
    private double velY = 4.0;
    private int flashFrames = 0; // $Asad$ Tracks how long the ball will stay white

    public Ball(int x, int y, int size, Color color) {
        super(x, y, size, size, color);
    }

    public void update() {
        x += velX;
        y += velY;
    }

    public void reverseX() { velX *= -1.05; } // Speed up slightly on hit
    public void reverseY() { velY *= -1; }

    public void reset(int screenWidth, int screenHeight) {
        x = screenWidth / 2 - width / 2;
        y = screenHeight / 2 - height / 2;
        velX = (Math.random() > 0.5) ? 4 : -4; // Random initial direction
    }

    @Override
    public void draw(Graphics g) {
        if (flashFrames > 0) // $Asad$
        {
            g.setColor(Color.WHITE);
            flashFrames--; // $Asad$ Count down each frame
        } else // $Asad$
        {
            g.setColor(color); // $Asad$ Back to normal neon yellow
        }
        g.fillOval((int)x, (int)y, width, height);
    }

    public double getX() { return x; }
    public double getY() { return y; }
    
    public void triggerFlash() // $Asad$
    {
        this.flashFrames = 25; // $Asad$ Flash lasts for 25 frames (1 second = 50 frames)
    }

}

---

import java.awt.*;

public abstract class GameObject {
    protected double x, y; // Use double for smoother sub-pixel movement
    protected int width, height;
    protected Color color;

    public GameObject(double x, double y, int width, int height, Color color) {
        this.x = x;
        this.y = y;
        this.width = width;
        this.height = height;
        this.color = color;
    }

    // Every object must define how it draws itself
    public abstract void draw(Graphics g);

    // Standard collision box
    public Rectangle getBounds() {
        return new Rectangle((int)x, (int)y, width, height);
    }
}

---

import java.awt.*;

public class Paddle extends GameObject {
    private static final int SPEED = 7;

    public Paddle(double x, double y, int width, int height, Color color) {
        super(x, y, width, height, color);
    }

    public void moveUp() { y -= SPEED; }
    public void moveDown() { y += SPEED; }

    // Keep paddle within the window frame
    public void clamp(int screenHeight) {
        if (y < 0) y = 0;
        if (y > screenHeight - height) y = screenHeight - height;
    }

    @Override
    public void draw(Graphics g) {
        g.setColor(color);
        g.fillRoundRect((int)x, (int)y, width, height, 10, 10); // Rounded for style
    }
}

---

import javax.swing.*;
import java.awt.*;
import javax.swing.JOptionPane; // $Asad$

public class PongGame extends JFrame {
    public PongGame() {
       
        // $Asad$ Create a variable to hold the name, defaulting to Guest
        String user = "Guest"; 
        
        // $Asad$ Create an array of text options that will turn into clickable buttons in the popup
        String[] options = {"Login", "Register", "Guest Mode"};
        
        // $Asad$ Open a popup box with the 3 buttons. 
        // $Asad$ It returns a number based on what was clicked: 0 = Login, 1 = Register, 2 = Guest Mode
        int choice = JOptionPane.showOptionDialog(null, "Welcome to Paddle Tron! Choose an option:", "Authentication", JOptionPane.DEFAULT_OPTION, JOptionPane.QUESTION_MESSAGE, null, options, options[0]
        );
        
        // $Asad$ Check if the user clicked either the "Login" (0) or "Register" (1) button
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
            // $Asad$Skip everything and just launch the game directly as a Guest
            JOptionPane.showMessageDialog(null, "Starting game in Guest Mode.");
        } 

        
        
        setTitle("Neon Pong - Java Edition");
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLayout(new BorderLayout());

        PongPanel gamePanel = new PongPanel();
        add(gamePanel, BorderLayout.CENTER);

        // Control Toolbar
        JPanel controls = new JPanel();
        controls.setBackground(Color.DARK_GRAY);

        JButton pauseBtn = new JButton("Pause/Resume");
        pauseBtn.setFocusable(false); // Crucial: don't steal focus from game
        pauseBtn.addActionListener(e -> gamePanel.togglePause());

        JButton resetBtn = new JButton("Reset Score");
        resetBtn.setFocusable(false);
        resetBtn.addActionListener(e -> gamePanel.resetGame());

        controls.add(pauseBtn);
        controls.add(resetBtn);
        add(controls, BorderLayout.SOUTH);

        pack(); // Resize window to fit panel
        setLocationRelativeTo(null);
        setVisible(true);
    }

    public static void main(String[] args) {
        // Run UI tasks on the Event Dispatch Thread
        SwingUtilities.invokeLater(PongGame::new);
    }
}

---

import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import java.util.HashSet;
import java.util.Set;

public class PongPanel extends JPanel implements ActionListener {
    private final int WIDTH = 800, HEIGHT = 500;
    private Timer timer;
    private Ball ball;
    private Paddle leftPaddle, rightPaddle;
    private int leftScore = 0, rightScore = 0;
    private boolean paused = false;

    // Track keys currently pressed for simultaneous movement
    private final Set<Integer> pressedKeys = new HashSet<>();

    public PongPanel() {
        setPreferredSize(new Dimension(WIDTH, HEIGHT));
        setBackground(new Color(30, 30, 30));
        setFocusable(true);

        ball = new Ball(WIDTH/2, HEIGHT/2, 20, Color.YELLOW);
        leftPaddle = new Paddle(20, HEIGHT/2 - 50, 15, 100, new Color(0, 200, 255));
        rightPaddle = new Paddle(WIDTH - 35, HEIGHT/2 - 50, 15, 100, new Color(255, 50, 50));

        // Smooth key handling
        addKeyListener(new KeyAdapter() {
            public void keyPressed(KeyEvent e) { pressedKeys.add(e.getKeyCode()); }
            public void keyReleased(KeyEvent e) { pressedKeys.remove(e.getKeyCode()); }
        });

        timer = new Timer(16, this); // ~60 FPS
        timer.start();
    }

    public void togglePause() { paused = !paused; }
    
    public void resetGame() {
        leftScore = 0; rightScore = 0;
        ball.reset(WIDTH, HEIGHT);
    }

    @Override
    public void actionPerformed(ActionEvent e) {
        if (!paused) {
            updateLogic();
        }
        repaint();
    }

    private void updateLogic() {
        // Paddle movement
        if (pressedKeys.contains(KeyEvent.VK_W)) leftPaddle.moveUp();
        if (pressedKeys.contains(KeyEvent.VK_S)) leftPaddle.moveDown();
        if (pressedKeys.contains(KeyEvent.VK_UP)) rightPaddle.moveUp();
        if (pressedKeys.contains(KeyEvent.VK_DOWN)) rightPaddle.moveDown();

        leftPaddle.clamp(HEIGHT);
        rightPaddle.clamp(HEIGHT);

        ball.update();

        // Wall collisions
        if (ball.getY() <= 0 || ball.getY() >= HEIGHT - 20) ball.reverseY();

        // Paddle collisions
        if (ball.getBounds().intersects(leftPaddle.getBounds()) || 
            ball.getBounds().intersects(rightPaddle.getBounds())) {
            ball.reverseX();
            ball.triggerFlash(); // $Asad$ To trigger flash white ball
        }

        // Scoring
        if (ball.getX() < 0) { rightScore++; ball.reset(WIDTH, HEIGHT); }
        if (ball.getX() > WIDTH) { leftScore++; ball.reset(WIDTH, HEIGHT); }
        
        if (ball.x < 0) 
        { 
            rightScore++; 
            ScoreSystem.updateHighScore(rightScore); // <-- CONNECTOR: Checks if Right Player set a record
            ball.reset(WIDTH, HEIGHT); 
        }
        if (ball.x > WIDTH) 
        { 
            leftScore++; 
            ScoreSystem.updateHighScore(leftScore);  // <-- CONNECTOR: Checks if Left Player set a record
            ball.reset(WIDTH, HEIGHT); 
        }
    }

    @Override
    protected void paintComponent(Graphics g) {
        super.paintComponent(g);
        
        // Draw Center Line
        g.setColor(new Color(255, 255, 255, 50));
        g.drawLine(WIDTH/2, 0, WIDTH/2, HEIGHT);

        ball.draw(g);
        leftPaddle.draw(g);
        rightPaddle.draw(g);

        // UI
        g.setColor(Color.WHITE);
        g.setFont(new Font("Arial", Font.BOLD, 40));
        g.drawString(String.valueOf(leftScore), WIDTH/2 - 60, 50);
        g.drawString(String.valueOf(rightScore), WIDTH/2 + 30, 50);

        if (paused) {
            g.drawString("PAUSED", WIDTH/2 - 80, HEIGHT/2);
        }
        
        g.setFont(new Font("Arial", Font.BOLD, 14));
        g.setColor(Color.CYAN);

        // CONNECTOR: Grabs the current highest score from the ScoreSystem file
        g.drawString("ALL-TIME RECORD: " + ScoreSystem.getGlobalHighScore(), 20, 20);
    }
}

---

import java.io.*;
import java.util.Scanner;

public class ScoreSystem {
    // The name of the file storing the single highest score value
    private static final String SCORE_FILE = "highscore.txt";

    // Reads and returns the top score currently saved on the machine
    public static int getGlobalHighScore() {
        try {
            File file = new File(SCORE_FILE);
            // If the file doesn't exist yet, the record is 0
            if (!file.exists()) return 0;
            
            // Open scanner to read the single integer stored in the file
            Scanner scanner = new Scanner(file);
            int high = scanner.nextInt();
            scanner.close();
            return high;
            
        } catch (Exception e) {
            return 0; // Fallback to 0 if the file is corrupted or empty
        }
    }

    // Compares the game's current score to the all-time record and updates it if beaten
    public static void updateHighScore(int currentScore) {
        // Only run if the player actually beat the previous high score
        if (currentScore > getGlobalHighScore()) {
            try {
                // Open FileWriter in overwrite mode (no 'true' argument)
                // This clears out the old number and replaces it with the brand new record
                FileWriter writer = new FileWriter(SCORE_FILE);
                writer.write(String.valueOf(currentScore));
                writer.close();
            } catch (IOException e) {
                System.out.println("Error saving high score.");
            }
        }
    }
}
