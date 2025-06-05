# Hanoi_GUI_1_-
import javax.swing.*;
import java.awt.*;
import java.util.*;
import java.util.List;

public class HanoiGUI extends JPanel {

    private final int numDisks = 3;
    private final List<Stack<Integer>> rods = Arrays.asList(new Stack<>(), new Stack<>(), new Stack<>());
    private final List<Color> colors = Arrays.asList(Color.BLUE, Color.YELLOW, Color.GREEN);
    private final int rodWidth = 20;
    private final int diskHeight = 10;

    public HanoiGUI() {
        for (int i = numDisks; i >= 1; i--) {
            rods.get(0).push(i);
        }

        // Fix: Moved thread inside constructor block
        new Thread(() -> {
            try {
                Thread.sleep(1000);
                solveHanoi(numDisks, 0, 1, 2);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }).start();
    }

    private void solveHanoi(int n, int src, int helper, int dest) throws InterruptedException {
        if (n == 1) {
            moveDisk(src, dest);
            return;
        }
        solveHanoi(n - 1, src, dest, helper);
        moveDisk(src, dest);
        solveHanoi(n - 1, helper, src, dest);
    }

    private void moveDisk(int from, int to) throws InterruptedException {
        int disk = rods.get(from).pop();
        rods.get(to).push(disk);
        repaint();
        Thread.sleep(1000);
    }

    @Override
    protected void paintComponent(Graphics g) {
        super.paintComponent(g);

        int panelWidth = getWidth();
        int panelHeight = getHeight();
        int rodXSpacing = panelWidth / 4;
        int baseY = panelHeight - 50;

        // Draw rods
        for (int i = 0; i < 3; i++) {
            int x = (i + 1) * rodXSpacing;
            g.setColor(Color.BLACK);
            g.fillRect(x - rodWidth / 2, baseY - 150, rodWidth, 150);
        }

        // Draw disks
        for (int i = 0; i < 3; i++) {
            Stack<Integer> rod = rods.get(i);
            int xCenter = (i + 1) * rodXSpacing;
            for (int j = 0; j < rod.size(); j++) {
                int diskSize = rod.get(j);
                int diskWidth = diskSize * 30;
                int x = xCenter - diskWidth / 2;
                int y = baseY - (j + 1) * diskHeight;
                g.setColor(colors.get((diskSize - 1) % colors.size()));
                g.fillRect(x, y, diskWidth, diskHeight);
                g.setColor(Color.BLACK);
                g.drawRect(x, y, diskWidth, diskHeight);
            }
        }
    }

    public static void main(String[] args) {
        JFrame frame = new JFrame("Tower of Hanoi - GUI Simulation");
        HanoiGUI hanoiPanel = new HanoiGUI();
        frame.add(hanoiPanel);
        frame.setSize(600, 400);
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setVisible(true);
    }
}
