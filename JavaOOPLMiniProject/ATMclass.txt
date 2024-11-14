package JavaOOPLMiniproject;

import java.util.Scanner;

public class ATM {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        Bank theBank = new Bank("Bank of Pccoe");

        // Create multiple users
        User user1 = theBank.addUser("John", "Doe", "1234", "317432");
        User user2 = theBank.addUser("Jane", "Smith", "5678", "123456");

        // Add accounts for users
        Account user1Checking = new CheckingAccount("Checking1", user1, theBank);
        Account user2Checking = new CheckingAccount("Checking1", user2, theBank);
        Account user1Savings = new SavingsAccount("Savings1", user1, theBank);
        Account user1Business = new BusinessAccount("Business1", user1, theBank);
        Account user2Savings = new SavingsAccount("Savings2", user1, theBank);
        Account user2Business = new BusinessAccount("Business2", user2, theBank);

        user1.addAccount(user1Checking);
        user1.addAccount(user1Savings);// Adding Savings account
        user2.addAccount(user2Savings);
        user2.addAccount(user2Checking);
        user1.addAccount(user1Business);
        user2.addAccount(user2Business); // Adding Business account

        theBank.addAccount(user1Checking);
        theBank.addAccount(user1Savings);// Adding Savings account to bank
        theBank.addAccount(user1Business);
        theBank.addAccount(user2Savings);
        theBank.addAccount(user2Checking);
        theBank.addAccount(user2Business); // Adding Business account to bank

        // Simulate user login
        User loggedInUser = ATM.mainMenuPrompt(theBank, sc);

        if (loggedInUser != null) {
            System.out.println("Login successful for user: " + loggedInUser.getUserID());
            // If login is successful, display the user menu
            ATM.printUserMenu(loggedInUser, sc);
        } else {
            System.out.println("Login failed.");
        }
    }

    public static User mainMenuPrompt(Bank theBank, Scanner sc) {
        String userID, pin;
        User authUser;

        do {
            System.out.printf("\n\nWelcome to %s\n\n", theBank.getName());
            System.out.print("Enter User ID: ");
            userID = sc.nextLine();
            System.out.print("Enter PIN: ");
            pin = sc.nextLine();

            authUser = theBank.userLogin(userID, pin);

            if (authUser == null) {
                System.out.println("Entered User ID: " + userID + " and PIN: " + pin);
                System.out.println("Incorrect user ID / PIN combination. Please try again.");
            }
        } while (authUser == null);

        return authUser;
    }

    public static void printUserMenu(User theUser, Scanner sc) {
        int choice;

        // Display the user's accounts summary
        theUser.printAccountsSummary();

        // User menu
        do {
            System.out.printf("Welcome %s, what would you like to do?\n", theUser.getFirstName());
            System.out.println("1) Show account transaction history");
            System.out.println("2) Withdraw");
            System.out.println("3) Deposit");
            System.out.println("4) Transfer");
            System.out.println("5) Quit");
            System.out.print("Enter choice: ");
            choice = sc.nextInt();

            if (choice < 1 || choice > 5) {
                System.out.println("Invalid choice, please choose between 1-5.");
            }
        } while (choice < 1 || choice > 5);

        // Process the user's choice
        switch (choice) {
            case 1 -> ATM.showTransHistory(theUser, sc);
            case 2 -> ATM.withdrawFunds(theUser, sc);
            case 3 -> ATM.depositFunds(theUser, sc);
            case 4 -> ATM.transferFunds(theUser, sc);
            case 5 -> System.out.println("Goodbye!");
        }

        // Redisplay the menu unless the user wants to quit
        if (choice != 5) {
            ATM.printUserMenu(theUser, sc);
        }
    }

    // Show transaction history
    public static void showTransHistory(User theUser, Scanner sc) {
        System.out.println("Showing transaction history...");
        System.out.println("Enter the account index to show transaction history: ");
        int index = sc.nextInt(); // Assume the user chooses an index from a list of accounts
        Account chosenAccount = theUser.getAccounts().get(index);
        chosenAccount.showTransactionHistory();
    }

    // Withdraw funds from the selected account
    public static void withdrawFunds(User theUser, Scanner sc) {
        System.out.println("Enter the account index to withdraw from: ");
        int index = sc.nextInt();
        Account chosenAccount = theUser.getAccounts().get(index);

        System.out.println("Enter amount to withdraw: ");
        double amount = sc.nextDouble();
        System.out.println("Enter a memo for this transaction: ");
        sc.nextLine(); // Consume newline
        String memo = sc.nextLine();

        chosenAccount.withdraw(amount, memo);
    }

    // Deposit funds into the selected account
    public static void depositFunds(User theUser, Scanner sc) {
        // Prompt the user to select an account
        System.out.print("Enter the account index to deposit into: ");
        int accountIndex = sc.nextInt();
        sc.nextLine(); // consume the newline left by nextInt

        // Check if the entered index is valid
        if (accountIndex >= 0 && accountIndex < theUser.getAccounts().size()) {
            Account selectedAccount = theUser.getAccounts().get(accountIndex);

            // Now prompt for the deposit amount
            System.out.print("Enter the amount to deposit: ");
            double amount = sc.nextDouble();
            sc.nextLine(); // consume the newline left by nextDouble()

            // Prompt for a memo (optional)
            System.out.print("Enter a memo: ");
            String memo = sc.nextLine();

            // Perform the deposit
            selectedAccount.deposit(amount, memo);
            System.out.printf("Deposited %.2f to account %s.\n", amount, selectedAccount.getAccountUUID());
        } else {
            System.out.println("Invalid account index. Please try again.");
        }
    }

    // Transfer funds between two accounts
    public static void transferFunds(User theUser, Scanner sc) {
        System.out.println("Enter the source account index: ");
        int sourceIndex = sc.nextInt();
        Account sourceAccount = theUser.getAccounts().get(sourceIndex);

        System.out.println("Enter the target account index: ");
        int targetIndex = sc.nextInt();
        Account targetAccount = theUser.getAccounts().get(targetIndex);

        System.out.println("Enter the amount to transfer: ");
        double amount = sc.nextDouble();

        System.out.println("Enter a memo for this transaction: ");
        sc.nextLine(); // Consume newline
        String memo = sc.nextLine();

        sourceAccount.transfer(targetAccount, amount);
    }
}
