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
