# BrainTeaser-A-Puzzle-Game
#The objective of the "BrainTeaser" Java program is to create a simple puzzle game where the player needs to rearrange numbered buttons in ascending order (1 through 8), leaving one space blank to allow movement.

import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.util.Collections;
import java.util.ArrayList;

public class BrainTeaser extends JFrame implements ActionListener {
    private JButton[] buttons = new JButton[9];
    private JPanel panel;

    public BrainTeaser() {
        setTitle("BrainTeaser Puzzle");
        setSize(300, 300);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLayout(new BorderLayout());

        panel = new JPanel();
        panel.setLayout(new GridLayout(3, 3));
        add(panel, BorderLayout.CENTER);

        initializeButtons();
        shuffleButtons();
        
        setVisible(true);
    }

    private void initializeButtons() {
        ArrayList<String> labels = new ArrayList<>();
        for (int i = 1; i <= 8; i++) {
            labels.add(String.valueOf(i));
        }
        labels.add(""); // Blank space

        for (int i = 0; i < 9; i++) {
            buttons[i] = new JButton(labels.get(i));
            buttons[i].setFont(new Font("Arial", Font.BOLD, 24));
            buttons[i].addActionListener(this);
            panel.add(buttons[i]);
        }
    }

    private void shuffleButtons() {
        ArrayList<String> values = new ArrayList<>();
        for (JButton button : buttons) {
            values.add(button.getText());
        }

        do {
            Collections.shuffle(values);
        } while (!isSolvable(values));

        for (int i = 0; i < 9; i++) {
            buttons[i].setText(values.get(i));
        }
    }

    private boolean isSolvable(ArrayList<String> values) {
        int inversions = 0;
        for (int i = 0; i < values.size(); i++) {
            for (int j = i + 1; j < values.size(); j++) {
                if (!values.get(i).isEmpty() && !values.get(j).isEmpty() &&
                    Integer.parseInt(values.get(i)) > Integer.parseInt(values.get(j))) {
                    inversions++;
                }
            }
        }
        return inversions % 2 == 0;
    }

    @Override
    public void actionPerformed(ActionEvent e) {
        JButton clickedButton = (JButton) e.getSource();
        int emptyIndex = findEmptyIndex();
        int clickedIndex = findButtonIndex(clickedButton);

        if (isAdjacent(emptyIndex, clickedIndex)) {
            buttons[emptyIndex].setText(clickedButton.getText());
            clickedButton.setText("");

            if (checkWin()) {
                JOptionPane.showMessageDialog(this, "Congratulations! You solved the puzzle.");
                shuffleButtons();
            }
        }
    }

    private int findEmptyIndex() {
        for (int i = 0; i < buttons.length; i++) {
            if (buttons[i].getText().isEmpty()) {
                return i;
            }
        }
        return -1;
    }

    private int findButtonIndex(JButton button) {
        for (int i = 0; i < buttons.length; i++) {
            if (buttons[i] == button) {
                return i;
            }
        }
        return -1;
    }

    private boolean isAdjacent(int emptyIndex, int clickedIndex) {
        int rowEmpty = emptyIndex / 3, colEmpty = emptyIndex % 3;
        int rowClicked = clickedIndex / 3, colClicked = clickedIndex % 3;
        return Math.abs(rowEmpty - rowClicked) + Math.abs(colEmpty - colClicked) == 1;
    }

    private boolean checkWin() {
        for (int i = 0; i < 8; i++) {
            if (!buttons[i].getText().equals(String.valueOf(i + 1))) {
                return false;
            }
        }
        return buttons[8].getText().isEmpty();
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(BrainTeaser::new);
    }
}
