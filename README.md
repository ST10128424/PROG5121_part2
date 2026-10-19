# PROG5121_part2
Part2 submission
package com.mycompany.prog5121prt2;

import javax.swing.JOptionPane;


public class PROG5121prt2 {
    
    private static String registeredUsername;
    private static String registeredPassword;
    private static String registeredPhone;

    public static void main(String[] args) {
        JOptionPane.showMessageDialog(null, "Welcome to QuickChat.");

        if (!registerUser() || !loginUser()) {
            JOptionPane.showMessageDialog(null, "Login failed. Exiting app.");
            return;
        }

        JOptionPane.showMessageDialog(null, "Login successful. Redirecting to QuickChat...");
        runMessageSystem();
    }

    private static boolean registerUser() {
        while (true) {
            registeredUsername = JOptionPane.showInputDialog(
                    "Create a username (must contain _ and be no more than 5 characters):");
            if (registeredUsername == null) {
                return false;
            }
            if (!registeredUsername.contains("_") || registeredUsername.length() > 5) {
                JOptionPane.showMessageDialog(null,
                        "Invalid username. It must contain _ and be no more than 5 characters.");
                continue;
            }

            registeredPassword = JOptionPane.showInputDialog("Create a password:");
            if (registeredPassword == null) {
                return false;
            }
            if (registeredPassword.isEmpty()) {
                JOptionPane.showMessageDialog(null, "Password cannot be empty.");
                continue;
            }

            registeredPhone = JOptionPane.showInputDialog(
                    "Enter your phone number (+27 followed by 9 digits):");
            if (registeredPhone == null) {
                return false;
            }
            if (!Message.isValidRecipient(registeredPhone)) {
                JOptionPane.showMessageDialog(null,
                        "Cell phone number is incorrectly formatted or does not contain an international code. Please correct the number and try again.");
                continue;
            }

            JOptionPane.showMessageDialog(null, "Registration successful!");
            return true;
        }
    }

    private static boolean loginUser() {
        String username = JOptionPane.showInputDialog("Login - Enter your username:");
        if (username == null) {
            return false;
        }
        String password = JOptionPane.showInputDialog("Enter your password:");
        return password != null
                && username.equals(registeredUsername)
                && password.equals(registeredPassword);
    }

    private static void runMessageSystem() {
        int messageLimit;
        while (true) {
            String input = JOptionPane.showInputDialog("How many messages would you like to enter?");
            if (input == null) {
                return;
            }
            try {
                messageLimit = Integer.parseInt(input);
                if (messageLimit > 0) {
                    break;
                }
            } catch (NumberFormatException ignored) {
                // Show the same validation message for non-numeric input.
            }
            JOptionPane.showMessageDialog(null, "Please enter a whole number greater than zero.");
        }

        int messagesEntered = 0;
        while (true) {
            String menu = """
                          Choose an option:
                          1) Send Messages
                          2) Show recently sent messages
                          3) Quit
                          Messages entered: """ + messagesEntered + "/" + messageLimit;
            String input = JOptionPane.showInputDialog(menu);
            if (input == null) {
                break;
            }

            switch (input.trim()) {
                case "1" -> {
                    if (messagesEntered >= messageLimit) {
                        JOptionPane.showMessageDialog(null, "You have entered the selected number of messages.");
                        break;
                    }
                    if (sendOneMessage(messagesEntered)) {
                        messagesEntered++;
                        if (messagesEntered == messageLimit) {
                            JOptionPane.showMessageDialog(null,
                                    Message.returnTotalMessagess()
                                            + "All messages have been entered. Total messages sent: ");
                        }
                    }
                }
                case "2" -> JOptionPane.showMessageDialog(null, "Coming Soon.");
                case "3" -> {
                    JOptionPane.showMessageDialog(null, "Goodbye!");
                    return;
                }
                default -> JOptionPane.showMessageDialog(null, "Please select 1, 2, or 3.");
            }
        }
    }

    private static boolean sendOneMessage(int messageNumber) {
        String recipient;
        while (true) {
            recipient = JOptionPane.showInputDialog(
                    "Enter recipient number (+27 followed by 9 digits, or a local number such as 08575975889):");
            if (recipient == null) {
                return false;
            }
            Message candidate = new Message(recipient, "");
            String validation = candidate.checkRecipientCell();
            if (Message.isValidRecipient(recipient)) {
                JOptionPane.showMessageDialog(null, validation);
                break;
            }
            JOptionPane.showMessageDialog(null,
                    "Cell phone number is incorrectly formatted or does not contain an international code. Please correct the number and try again.");
        }

        String text;
        while (true) {
            text = JOptionPane.showInputDialog("Enter message (maximum 250 characters):");
            if (text == null) {
                return false;
            }
            if (text.length() <= 250 && !text.trim().isEmpty()) {
                JOptionPane.showMessageDialog(null, "Message ready to send.");
                break;
            }
            if (text.length() > 250) {
                JOptionPane.showMessageDialog(null,
                        "Message exceeds 250 characters by " + (text.length() - 250)
                                + "; please reduce the size.\nPlease enter a message of less than 250 characters.");
            } else {
                JOptionPane.showMessageDialog(null, "Message cannot be empty.");
            }
        }

        Message message = new Message(recipient, text);
        message.setMessageNumber(messageNumber);
        JOptionPane.showMessageDialog(null, "Message ID generated: " + message.getMessageID());
        JOptionPane.showMessageDialog(null, "Message Hash: " + message.createMessageHash());

        String result = message.SentMessage();
        JOptionPane.showMessageDialog(null, result);
        if ("Message successfully sent.".equals(result)) {
            JOptionPane.showMessageDialog(null, message.getDetails());
        }
        return true;
    }
}

