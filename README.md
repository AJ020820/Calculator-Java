# Calculator-Java
calculator using java and awt with database connenection

import java.awt.*;
import java.awt.event.*;
import java.sql.*;

public class Calculatorevision extends Frame {
    private TextField num1Field;
    private TextField num2Field;
    private TextField resultField;
    private Connection connection;

    public Calculatorevision() {
        setTitle("Calculator");
        setSize(300, 200);
        setLayout(new FlowLayout());

        Label num1Label = new Label("Number 1:");
        num1Field = new TextField(10);

        Label num2Label = new Label("Number 2:");
        num2Field = new TextField(10);

        Button addButton = new Button("Add");
        resultField = new TextField(15);
        resultField.setEditable(false);

        addButton.addActionListener(new ActionListener() {
            public void actionPerformed(ActionEvent e) {
                calculateSum();
            }
        });

        add(num1Label);
        add(num1Field);
        add(num2Label);
        add(num2Field);
        add(addButton);
        add(resultField);

        addWindowListener(new WindowAdapter() {
            public void windowClosing(WindowEvent e) {
                closeConnection();
                System.exit(0);
            }
        });

        establishConnection();
    }

    private void establishConnection() {
        String url = "jdbc:mysql://localhost:3306/test";
        String username = "root";
        String password = "";

        try {
            connection = DriverManager.getConnection(url, username, password);
            System.out.println("Connected to the database");
        } catch (SQLException e) {
            System.out.println("Failed to connect to the database");
            e.printStackTrace();
        }
    }

    private void closeConnection() {
        try {
            if (connection != null && !connection.isClosed()) {
                connection.close();
                System.out.println("Disconnected from the database");
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    private void calculateSum() {
        try {
            int num1 = Integer.parseInt(num1Field.getText());
            int num2 = Integer.parseInt(num2Field.getText());
            int sum = num1 + num2;
            resultField.setText(String.valueOf(sum));

            // Insert the calculation into the database
            String query = "INSERT INTO calculations (num1, num2, result) VALUES (?, ?, ?)";
            PreparedStatement statement = connection.prepareStatement(query);
            statement.setInt(1, num1);
            statement.setInt(2, num2);
            statement.setInt(3, sum);
            statement.executeUpdate();
        } catch (NumberFormatException ex) {
            resultField.setText("Invalid input");
        } catch (SQLException ex) {
            ex.printStackTrace();
        }
    }

    public static void main(String[] args) {
        Calculatorevision calculator = new Calculatorevision();
        calculator.setVisible(true);
    }
}
