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
