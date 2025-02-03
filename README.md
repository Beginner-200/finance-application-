package com.personalfinance;

import com.personalfinance.model.*;
import com.personalfinance.model.CheckingAccount;

import com.personalfinance.service.AccountManager;
import java.util.Date;

public class copyofmain {
    public static void main(String[] args) {
        User user = new User("1", "John Doe", "john.doe@example.com");
        Account account = new CheckingAccount("101", 500.0);

        Transaction deposit = new Transaction("T1001", new Date(), 200.0, "Salary deposit");
        Transaction withdrawal = new Transaction("T1002", new Date(), -100.0, "ATM withdrawal");

        AccountManager manager = new AccountManager();

        try {
            manager.processTransaction(account, deposit);
            System.out.println("New balance after deposit: $" + account.getBalance());

            manager.processTransaction(account, withdrawal);
            System.out.println("New balance after withdrawal: $" + account.getBalance());
        } catch (Exception e) {
            System.err.println("Transaction failed: " + e.getMessage());
        }
    }
}





package com.personalfinance;

import java.sql.*;
import java.util.Scanner;

public class Main {
    private static final String DB_URL = "jdbc:postgresql://localhost:5432/postgres";
    private static final String DB_USER = "postgres";
    private static final String DB_PASSWORD = "123456";

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        System.out.print("Enter Name: ");
        String name = scanner.nextLine();
        System.out.print("Enter Amount to Deposit: ");
        double amount = scanner.nextDouble();

        try (Connection con = DriverManager.getConnection(DB_URL, DB_USER, DB_PASSWORD);
             PreparedStatement pstmt = con.prepareStatement("INSERT INTO accounts (name, balance) VALUES (?, ?)");) {
            pstmt.setString(1, name);
            pstmt.setDouble(2, amount);
            pstmt.executeUpdate();
            System.out.println("Account created successfully with balance: $" + amount);
        } catch (SQLException e) {
            System.err.println("Error: " + e.getMessage());
        }
    }
}


package com.personalfinance;
import java.sql.Connection;
import java.sql.DriverManager;

public class PostgreSQLJDBC {
    public static void main(String args[]) {
        Connection c = null;
        try {
            Class.forName("org.postgresql.Driver");
            c = DriverManager
                    .getConnection("jdbc:postgresql://localhost:5432/postgres",
                            "postgres", "123456");
        } catch (Exception e) {
            e.printStackTrace();
            System.err.println(e.getClass().getName()+": "+e.getMessage());
            System.exit(0);
        }
        System.out.println("Opened database successfully");
    }
}

package com.personalfinance.model;

public class User {
    private String id;
    private String name;
    private String email;
    private String password; // Ensure this field exists

    // Constructor
    public User(String id, String name, String email, String password) {
        this.id = id;
        this.name = name;
        this.email = email;
        this.password = password;
    }

    // Getters
    public String getId() { return id; }
    public String getName() { return name; }
    public String getEmail() { return email; }
    public String getPassword() { return password; } // Add this method

    public void setEmail(String email) { this.email = email; }
    public void setPassword(String password) { this.password = password; }
}


package com.personalfinance.model;

public abstract class Account {
    protected String accountId;
    protected double balance;

    public Account(String accountId, double balance) {
        this.accountId = accountId;
        this.balance = balance;
    }

    public abstract void deposit(double amount);
    public abstract void withdraw(double amount) throws Exception;

    public String getAccountId() { return accountId; }
    public double getBalance() { return balance; }
}

package com.personalfinance.model;

public class CheckingAccount extends Account {
    public CheckingAccount(String accountId, double balance) {
        super(accountId, balance);
    }


    public void deposit(double amount) {
        this.balance += amount;
    }


    public void withdraw(double amount) throws Exception {
        if (this.balance < amount) {
            throw new Exception("Insufficient funds");
        }
        this.balance -= amount;
    }
}


package com.personalfinance.model;

import java.util.Date;

public class Transaction {
    private String transactionId;
    private Date transactionDate;
    private double amount;
    private String description;

    public Transaction(String transactionId, Date transactionDate, double amount, String description) {
        this.transactionId = transactionId;
        this.transactionDate = transactionDate;
        this.amount = amount;
        this.description = description;
    }

    // Getters
    public String getTransactionId() { return transactionId; }
    public Date getTransactionDate() { return transactionDate; }
    public double getAmount() { return amount; }
    public String getDescription() { return description; }
}


package com.personalfinance.service;


import com.personalfinance.model.Account;
import com.personalfinance.model.Transaction;

public class AccountManager {
    public void processTransaction(Account account, Transaction transaction) throws Exception {
        if (transaction.getAmount() < 0) {
            account.withdraw(-transaction.getAmount());
        } else {
            account.deposit(transaction.getAmount());
        }
    }
}

