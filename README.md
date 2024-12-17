# Faculty-Feedback-System
Faculty Feedback System using java

import java.awt.*;
import java.awt.event.*;
import java.util.ArrayList;
import java.util.HashSet;
import java.util.List;
import java.util.Set;
import javax.swing.*;

public class FacultyFeedbackSystem extends JFrame implements ActionListener {

    // Components
    private JLabel lblWelcome, lblName, lblUSN, lblMentor, lblSubject;
    private JLabel lblQuestion1, lblQuestion2, lblQuestion3, lblQuestion4;
    private JTextField txtName, txtUSN;
    private JButton btnSubmit, btnClear, btnExit, btnDisplay, btnAdminLogin, btnDisplayTeacherFeedback;

    private JComboBox<String> question1Dropdown, question2Dropdown, question3Dropdown, question4Dropdown;
    private JComboBox<String> teacherSelectionDropdown;

    // Data
    private final String[] teachers = {
            "Maths M-3 by Dange Sir",
            "DDCO by Sunanda Mam",
            "OS by Gurudevi Mam",
            "DSA by Poornima Mam",
            "JAVA by Hema Mam"
    };
    private int currentTeacherIndex = 0;
    private int currentStudentIndex = 0;

    // Maximum number of students allowed
    private static final int MAX_STUDENTS = 70;

    // List to store feedback for up to 70 students
    private final List<ArrayList<String>> allFeedback = new ArrayList<>();
    private final Set<String> usedUSNs = new HashSet<>();

    // Admin Credentials
    private static final String ADMIN_USERNAME = "AIML";
    private static final String ADMIN_PASSWORD = "ROOT123";
    private boolean isAdminLoggedIn = false;

    // Constructor
    public FacultyFeedbackSystem() {
        super("Faculty Feedback System");
        setLayout(null);
        setSize(700, 600);
        setLocationRelativeTo(null);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        // Initialize Components
        lblWelcome = new JLabel("Faculty Feedback System", SwingConstants.CENTER);
        lblWelcome.setFont(new Font("Arial", Font.BOLD, 20));
        lblWelcome.setBounds(100, 20, 500, 30);

        lblName = new JLabel("Name:");
        lblName.setBounds(50, 70, 100, 20);
        txtName = new JTextField();
        txtName.setBounds(150, 70, 400, 20);

        lblUSN = new JLabel("USN:");
        lblUSN.setBounds(50, 110, 100, 20);
        txtUSN = new JTextField();
        txtUSN.setBounds(150, 110, 400, 20);

        lblMentor = new JLabel("Mentor:");
        lblMentor.setBounds(50, 150, 400, 20);

        lblSubject = new JLabel("Subject:");
        lblSubject.setBounds(50, 190, 400, 20);

        // Questions
        lblQuestion1 = new JLabel("1. How was the teaching performance?");
        lblQuestion1.setBounds(50, 230, 400, 20);

        lblQuestion2 = new JLabel("2. Was the teacher available for doubts?");
        lblQuestion2.setBounds(50, 270, 400, 20);

        lblQuestion3 = new JLabel("3. How interactive was the class?");
        lblQuestion3.setBounds(50, 310, 400, 20);

        lblQuestion4 = new JLabel("4. How would you rate overall learning?");
        lblQuestion4.setBounds(50, 350, 400, 20);

        question1Dropdown = createRatingDropdown(450, 230);
        question2Dropdown = createRatingDropdown(450, 270);
        question3Dropdown = createRatingDropdown(450, 310);
        question4Dropdown = createRatingDropdown(450, 350);

        // Buttons
        btnSubmit = new JButton("Submit");
        btnSubmit.setBounds(50, 450, 100, 30);
        btnSubmit.setEnabled(false); // Initially disabled

        btnClear = new JButton("Clear");
        btnClear.setBounds(170, 450, 100, 30);

        btnDisplay = new JButton("Display Feedback");
        btnDisplay.setBounds(290, 450, 150, 30);

        btnExit = new JButton("Exit");
        btnExit.setBounds(450, 450, 100, 30);

        btnAdminLogin = new JButton("Admin Login");
        btnAdminLogin.setBounds(300, 500, 120, 30);

        btnDisplayTeacherFeedback = new JButton("Display Teacher Feedback");
        btnDisplayTeacherFeedback.setBounds(50, 500, 200, 30);

        // Add Components
        add(lblWelcome);
        add(lblName);
        add(txtName);
        add(lblUSN);
        add(txtUSN);
        add(lblMentor);
        add(lblSubject);
        add(lblQuestion1);
        add(question1Dropdown);
        add(lblQuestion2);
        add(question2Dropdown);
        add(lblQuestion3);
        add(question3Dropdown);
        add(lblQuestion4);
        add(question4Dropdown);
        add(btnSubmit);
        add(btnClear);
        add(btnDisplay);
        add(btnExit);
        add(btnAdminLogin);
        add(btnDisplayTeacherFeedback);

        // Add Teacher Selection Dropdown
        teacherSelectionDropdown = new JComboBox<>(teachers);
        teacherSelectionDropdown.setBounds(300, 450, 150, 30);
        teacherSelectionDropdown.setVisible(false); // Initially hidden
        add(teacherSelectionDropdown);

        // Action Listeners
        btnSubmit.addActionListener(this);
        btnClear.addActionListener(this);
        btnDisplay.addActionListener(this);
        btnExit.addActionListener(this);
        btnAdminLogin.addActionListener(this);
        btnDisplayTeacherFeedback.addActionListener(this);

        txtUSN.addActionListener(e -> displayMentor());

        displayNextTeacher();
        setVisible(true);
    }

    private JComboBox<String> createRatingDropdown(int x, int y) {
        JComboBox<String> dropdown = new JComboBox<>();
        for (int i = 1; i <= 10; i++) dropdown.addItem(String.valueOf(i));
        dropdown.setBounds(x, y, 100, 20);
        return dropdown;
    }

    private void displayMentor() {
        String usnText = txtUSN.getText().trim();

        if (usnText.matches("2BL23CI\\d{3}")) {
            int studentNumber = Integer.parseInt(usnText.substring(7)); // Extract last three digits

            if (studentNumber >= 1 && studentNumber <= 20) {
                lblMentor.setText("Mentor: Sana Mulla");
            } else if (studentNumber >= 21 && studentNumber <= 44) {
                lblMentor.setText("Mentor: Gurudevi Mam");
            } else if (studentNumber >= 45 && studentNumber <= 63) {
                lblMentor.setText("Mentor: Poornima Mamadapur");
            } else {
                lblMentor.setText("Mentor: Invalid USN range.");
            }

            btnSubmit.setEnabled(true); // Enable submission for valid USNs
        } else {
            lblMentor.setText("Mentor: Invalid USN format.");
            btnSubmit.setEnabled(false); // Disable submission for invalid USNs
        }
    }

    private void submitFeedback() {
        String name = txtName.getText().trim();
        String usn = txtUSN.getText().trim();
        if (name.isEmpty() || usn.isEmpty()) {
            JOptionPane.showMessageDialog(this, "Please enter both Name and USN!", "Error", JOptionPane.ERROR_MESSAGE);
            return;
        }
        if (usedUSNs.contains(usn)) {
            JOptionPane.showMessageDialog(this, "This USN has already submitted feedback!", "Error", JOptionPane.ERROR_MESSAGE);
            return;
        }

        if (currentStudentIndex >= MAX_STUDENTS) {
            JOptionPane.showMessageDialog(this, "Maximum student limit reached!", "Error", JOptionPane.ERROR_MESSAGE);
            return;
        }

        // Add feedback
        if (currentTeacherIndex == 0) {
            allFeedback.add(new ArrayList<>());
            allFeedback.get(currentStudentIndex).add("Name: " + name + ", USN: " + usn);
        }

        String feedback = String.format("Subject: %s, Q1: %s, Q2: %s, Q3: %s, Q4: %s",
                teachers[currentTeacherIndex],
                question1Dropdown.getSelectedItem(),
                question2Dropdown.getSelectedItem(),
                question3Dropdown.getSelectedItem(),
                question4Dropdown.getSelectedItem());
        allFeedback.get(currentStudentIndex).add(feedback);

        currentTeacherIndex++;
        if (currentTeacherIndex == teachers.length) {
            usedUSNs.add(usn);
            JOptionPane.showMessageDialog(this, "Thank you for submitting feedback!", "Complete", JOptionPane.INFORMATION_MESSAGE);
            currentStudentIndex++;
            clearSession();
        } else {
            displayNextTeacher();
        }
    }

    private void displayNextTeacher() {
        lblSubject.setText("Subject: " + teachers[currentTeacherIndex]);
        clearFields();
    }

    private void clearFields() {
        question1Dropdown.setSelectedIndex(0);
        question2Dropdown.setSelectedIndex(0);
        question3Dropdown.setSelectedIndex(0);
        question4Dropdown.setSelectedIndex(0);
    }

    private void clearSession() {
        currentTeacherIndex = 0;
        displayNextTeacher();
        btnSubmit.setEnabled(false);
    }

    private void displayAllFeedback() {
        if (!isAdminLoggedIn) {
            JOptionPane.showMessageDialog(this, "Access Denied! Admin login required.", "Error", JOptionPane.ERROR_MESSAGE);
            return;
        }
        if (allFeedback.isEmpty()) {
            JOptionPane.showMessageDialog(this, "No feedback available yet.", "Feedback List", JOptionPane.INFORMATION_MESSAGE);
            return;
        }

        StringBuilder feedbackDisplay = new StringBuilder("All Feedback:\n");
        for (int i = 0; i < allFeedback.size(); i++) {
            feedbackDisplay.append("Student ").append(i + 1).append(":\n");
            for (String feedback : allFeedback.get(i)) {
                feedbackDisplay.append(feedback).append("\n");
            }
            feedbackDisplay.append("\n");
        }
        JOptionPane.showMessageDialog(this, feedbackDisplay.toString(), "All Feedback", JOptionPane.INFORMATION_MESSAGE);
    }

    private void showAdminLoginDialog() {
        JPanel loginPanel = new JPanel(new GridLayout(2, 2));
        JTextField txtUsername = new JTextField();
        JPasswordField txtPassword = new JPasswordField();
        loginPanel.add(new JLabel("Username:"));
        loginPanel.add(txtUsername);
        loginPanel.add(new JLabel("Password:"));
        loginPanel.add(txtPassword);

        int option = JOptionPane.showConfirmDialog(this, loginPanel, "Admin Login", JOptionPane.OK_CANCEL_OPTION);
        if (option == JOptionPane.OK_OPTION) {
            String username = txtUsername.getText().trim();
            String password = new String(txtPassword.getPassword()).trim();
            if (ADMIN_USERNAME.equals(username) && ADMIN_PASSWORD.equals(password)) {
                isAdminLoggedIn = true;
                JOptionPane.showMessageDialog(this, "Login successful. You can now view feedback.", "Success", JOptionPane.INFORMATION_MESSAGE);
                teacherSelectionDropdown.setVisible(true); // Show teacher selection dropdown
            } else {
                JOptionPane.showMessageDialog(this, "Invalid credentials!", "Login Failed", JOptionPane.ERROR_MESSAGE);
            }
        }
    }

    private void displayTeacherFeedback() {
        if (!isAdminLoggedIn) {
            JOptionPane.showMessageDialog(this, "Access Denied! Admin login required.", "Error", JOptionPane.ERROR_MESSAGE);
            return;
        }

        String selectedTeacher = (String) teacherSelectionDropdown.getSelectedItem();
        StringBuilder feedbackDisplay = new StringBuilder("Feedback for " + selectedTeacher + ":\n");

        boolean feedbackFound = false;
        for (ArrayList<String> feedbackList : allFeedback) {
            for (String feedback : feedbackList) {
                if (feedback.contains(selectedTeacher)) {
                    feedbackDisplay.append(feedback).append("\n");
                    feedbackFound = true;
                }
            }
        }

        if (feedbackFound) {
            JOptionPane.showMessageDialog(this, feedbackDisplay.toString(), "Teacher Feedback", JOptionPane.INFORMATION_MESSAGE);
        } else {
            JOptionPane.showMessageDialog(this, "No feedback available for this teacher.", "No Feedback", JOptionPane.INFORMATION_MESSAGE);
        }
    }

    public static void main(String[] args) {
        new FacultyFeedbackSystem();
    }

    @Override
    public void actionPerformed(ActionEvent e) {
        if (e.getSource() == btnSubmit) submitFeedback();
        else if (e.getSource() == btnClear) clearSession();
        else if (e.getSource() == btnDisplay) displayAllFeedback();
        else if (e.getSource() == btnExit) System.exit(0);
        else if (e.getSource() == btnAdminLogin) showAdminLoginDialog();
        else if (e.getSource() == btnDisplayTeacherFeedback) displayTeacherFeedback();
    }
}
