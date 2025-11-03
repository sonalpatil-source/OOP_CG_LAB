import java.awt.*;
import java.awt.event.*;
import javax.swing.*;

public class PolygonFill extends JPanel implements ActionListener, MouseListener {
    int[] xPoints = {100, 200, 250, 150, 80};   // concave polygon vertices
    int[] yPoints = {100, 80, 200, 250, 180};
    int nPoints = xPoints.length;

    Color fillColor = Color.RED;
    int choice = 0; // 1=ScanLine, 2=FloodFill, 3=SeedFill

    JButton scanBtn, floodBtn, seedBtn, colorBtn;

    public PolygonFill() {
        setLayout(new FlowLayout());
        scanBtn = new JButton("Scan Line Fill");
        floodBtn = new JButton("Flood Fill");
        seedBtn = new JButton("Seed Fill");
        colorBtn = new JButton("Choose Color");

        add(scanBtn);
        add(floodBtn);
        add(seedBtn);
        add(colorBtn);

        scanBtn.addActionListener(this);
        floodBtn.addActionListener(this);
        seedBtn.addActionListener(this);
        colorBtn.addActionListener(this);

        addMouseListener(this);
    }

    // ------------------- Paint the Polygon ----------------------
    public void paintComponent(Graphics g) {
        super.paintComponent(g);
        g.setColor(Color.WHITE);
        g.fillRect(0, 0, getWidth(), getHeight());
        g.setColor(Color.BLACK);
        g.drawPolygon(xPoints, yPoints, nPoints);

        if (choice == 1) scanLineFill(g, xPoints, yPoints, nPoints);
        // flood & seed fill handled by mouse click
    }

    // ------------------- SCAN LINE FILL --------------------------
    void scanLineFill(Graphics g, int x[], int y[], int n) {
        int ymax = y[0], ymin = y[0];
        for (int i = 1; i < n; i++) {
            if (y[i] > ymax) ymax = y[i];
            if (y[i] < ymin) ymin = y[i];
        }

        g.setColor(fillColor);
        for (int scan = ymin; scan <= ymax; scan++) {
            int count = 0;
            int[] interX = new int[n];

            // find intersections with polygon edges
            for (int i = 0; i < n; i++) {
                int x1 = x[i], y1 = y[i];
                int x2 = x[(i + 1) % n], y2 = y[(i + 1) % n];

                if ((y1 <= scan && y2 > scan) || (y2 <= scan && y1 > scan)) {
                    interX[count++] = x1 + (int)((float)(scan - y1) / (y2 - y1) * (x2 - x1));
                }
            }

            // sort intersection points
            for (int i = 0; i < count - 1; i++)
                for (int j = 0; j < count - i - 1; j++)
                    if (interX[j] > interX[j + 1]) {
                        int temp = interX[j];
                        interX[j] = interX[j + 1];
                        interX[j + 1] = temp;
                    }

            // fill pixels between pairs
            for (int i = 0; i < count; i += 2) {
                if (i + 1 < count)
                    g.drawLine(interX[i], scan, interX[i + 1], scan);
            }
        }
    }

    // ------------------- FLOOD FILL -------------------------------
    void floodFill(int x, int y, Color targetColor, Color replacementColor, Graphics g) {
        try {
            Robot r = new Robot();
            Color current = r.getPixelColor(x + getLocationOnScreen().x, y + getLocationOnScreen().y);
            if (!current.equals(targetColor) && !current.equals(replacementColor)) return;
        } catch (Exception e) { return; }

        g.setColor(replacementColor);
        g.drawLine(x, y, x, y);

        floodFill(x + 1, y, targetColor, replacementColor, g);
        floodFill(x - 1, y, targetColor, replacementColor, g);
        floodFill(x, y + 1, targetColor, replacementColor, g);
        floodFill(x, y - 1, targetColor, replacementColor, g);
    }

    // ------------------- SEED FILL -------------------------------
    void seedFill(int x, int y, Color boundaryColor, Color fillColor, Graphics g) {
        try {
            Robot r = new Robot();
            Color current = r.getPixelColor(x + getLocationOnScreen().x, y + getLocationOnScreen().y);
            if (current.equals(boundaryColor) || current.equals(fillColor)) return;
        } catch (Exception e) { return; }

        g.setColor(fillColor);
        g.drawLine(x, y, x, y);

        seedFill(x + 1, y, boundaryColor, fillColor, g);
        seedFill(x - 1, y, boundaryColor, fillColor, g);
        seedFill(x, y + 1, boundaryColor, fillColor, g);
        seedFill(x, y - 1, boundaryColor, fillColor, g);
    }

    // ------------------- Menu Actions -----------------------------
    public void actionPerformed(ActionEvent e) {
        if (e.getSource() == scanBtn) {
            choice = 1;
            repaint();
        } else if (e.getSource() == floodBtn) {
            choice = 2;
            JOptionPane.showMessageDialog(this, "Click inside polygon to fill (Flood Fill)");
        } else if (e.getSource() == seedBtn) {
            choice = 3;
            JOptionPane.showMessageDialog(this, "Click inside polygon to fill (Seed Fill)");
        } else if (e.getSource() == colorBtn) {
            Color newColor = JColorChooser.showDialog(this, "Choose Fill Color", fillColor);
            if (newColor != null) fillColor = newColor;
            repaint();
        }
    }

    // ------------------- Mouse Listener ----------------------------
    public void mousePressed(MouseEvent e) {
        if (choice == 2) {
            Graphics g = getGraphics();
            floodFill(e.getX(), e.getY(), Color.WHITE, fillColor, g);
        } else if (choice == 3) {
            Graphics g = getGraphics();
            seedFill(e.getX(), e.getY(), Color.BLACK, fillColor, g);
        }
    }

    public void mouseReleased(MouseEvent e) {}
    public void mouseClicked(MouseEvent e) {}
    public void mouseEntered(MouseEvent e) {}
    public void mouseExited(MouseEvent e) {}

    // ------------------- Main Function ----------------------------
    public static void main(String[] args) {
        JFrame f = new JFrame("Polygon Fill Algorithms");
        PolygonFill panel = new PolygonFill();
        f.add(panel);
        f.setSize(600, 500);
        f.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        f.setVisible(true);
    }
}
