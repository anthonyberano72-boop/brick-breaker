import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import java.util.ArrayList;

class BrickBreaker extends JFrame {

    class GameBoard extends JPanel implements ActionListener, KeyListener {

        abstract class GameObject {
            int x, y, width, height;

            GameObject(int x, int y, int width, int height) {
                this.x = x;
                this.y = y;
                this.width = width;
                this.height = height;
            }

            Rectangle getBounds() {
                return new Rectangle(x, y, width, height);
            }

            abstract void draw(Graphics g);
        }

        class Paddle extends GameObject {
            int speed = 8;

            Paddle(int x, int y) {
                super(x, y, 80, 15);
            }

            void moveLeft() {
                x -= speed;
                if (x < 0) x = 0;
            }

            void moveRight(int boardWidth) {
                x += speed;
                if (x + width > boardWidth) x = boardWidth - width;
            }

            @Override
            void draw(Graphics g) {
                g.setColor(Color.BLUE);
                g.fillRect(x, y, width, height);
            }
        }

        class Ball extends GameObject {
            int dx = 4;
            int dy = -4;

            Ball(int x, int y) {
                super(x, y, 15, 15);
            }

            void update() {
                x += dx;
                y += dy;
            }

            @Override
            void draw(Graphics g) {
                g.setColor(Color.BLUE);
                g.fillOval(x, y, width, height);
            }
        }

        class Brick extends GameObject {
            boolean destroyed = false;
            Color color;

            Brick(int x, int y) {
                super(x, y, 60, 20);
                color = new Color(
                        (int)(Math.random() * 255),
                        (int)(Math.random() * 255),
                        (int)(Math.random() * 255)
                );
            }

            @Override
            void draw(Graphics g) {
                if (!destroyed) {
                    g.setColor(color);
                    g.fillRect(x, y, width, height);
                }
            }
        }

        Paddle paddle;
        Ball ball;
        ArrayList<Brick> bricks;
        Timer timer;
        boolean running = true;
        String message = "";

        GameBoard() {
            setBackground(Color.BLACK);
            setFocusable(true);
            addKeyListener(this);

            paddle = new Paddle(260, 450);
            ball = new Ball(295, 300);

            createBricks();

            timer = new Timer(16, this);
            timer.start();
        }

        void createBricks() {
            bricks = new ArrayList<>();
            int rows = 5, cols = 8;

            for (int r = 0; r < rows; r++) {
                for (int c = 0; c < cols; c++) {
                    bricks.add(new Brick(20 + c * 70, 30 + r * 30));
                }
            }
        }

        void restartGame() {
            paddle = new Paddle(260, 450);
            ball = new Ball(295, 300);
            createBricks();
            message = "";
            running = true;
            timer.start();
            repaint();
        }

        @Override
        public void actionPerformed(ActionEvent e) {
            if (!running) return;
            ball.update();
            checkCollisions();
            repaint();
        }

        void checkCollisions() {
            if (ball.x <= 0 || ball.x + ball.width >= getWidth())
                ball.dx *= -1;

            if (ball.y <= 0)
                ball.dy *= -1;

            if (ball.y + ball.height >= getHeight()) {
                running = false;
                timer.stop();
                message = "Game Over!";
            }

            if (ball.getBounds().intersects(paddle.getBounds())) {
                ball.dy *= -1;
                ball.y = paddle.y - ball.height;
            }

            boolean allDestroyed = true;

            for (Brick b : bricks) {
                if (!b.destroyed) {
                    allDestroyed = false;

                    if (ball.getBounds().intersects(b.getBounds())) {
                        b.destroyed = true;
                        ball.dy *= -1;
                    }
                }
            }

            if (allDestroyed) {
                running = false;
                timer.stop();
                message = "You Win!";
            }
        }

        @Override
        protected void paintComponent(Graphics g) {
            super.paintComponent(g);

            paddle.draw(g);
            ball.draw(g);

            for (Brick b : bricks)
                b.draw(g);

            if (!running) {
                g.setColor(Color.WHITE);
                g.setFont(new Font("Arial", Font.BOLD, 30));
                g.drawString(message, getWidth() / 2 - 70, getHeight() / 2);

                g.setFont(new Font("Arial", Font.PLAIN, 18));
                g.drawString("Press SPACE to play again",
                        getWidth() / 2 - 110, getHeight() / 2 + 40);
            }
        }

        @Override
        public void keyPressed(KeyEvent e) {
            if (running) {
                if (e.getKeyCode() == KeyEvent.VK_LEFT)
                    paddle.moveLeft();

                if (e.getKeyCode() == KeyEvent.VK_RIGHT)
                    paddle.moveRight(getWidth());
            }

            if (!running && e.getKeyCode() == KeyEvent.VK_SPACE) {
                restartGame();
            }
        }

        @Override public void keyReleased(KeyEvent e) {}
        @Override public void keyTyped(KeyEvent e) {}
    }

    public BrickBreaker() {
        setTitle("Brick Breaker");
        setSize(600, 500);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLocationRelativeTo(null);

        add(new GameBoard());
        setVisible(true);
    }

    public static void main(String[] args) {
        new BrickBreaker();
    }
}
