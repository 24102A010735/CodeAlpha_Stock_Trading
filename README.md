# CodeAlpha_Stock_Trading
import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.util.HashMap;
import java.util.Map;

public class StockTrading extends JFrame {
    // Simulated stock market data
    private Map<String, Double> stockPrices = new HashMap<>();
    // User's portfolio (stock symbol -> quantity)
    private Map<String, Integer> userPortfolio = new HashMap<>();
    // User's cash balance
    private double userBalance = 10000.0;

    // Text area to display output
    private JTextArea displayArea;

    public StockTrading() {
        setTitle("Stock Trading Simulator");
        setSize(600, 400);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLocationRelativeTo(null);

        // Initialize stock data
        initializeStocks();

        // Create a scroll able text area for output
        displayArea = new JTextArea();
        displayArea.setEditable(false);
        JScrollPane scrollPane = new JScrollPane(displayArea);

        // Buttons for actions
        JButton viewStocksButton = new JButton("View Stock Prices");
        JButton buyStocksButton = new JButton("Buy Stocks");
        JButton sellStocksButton = new JButton("Sell Stocks");
        JButton viewPortfolioButton = new JButton("View Portfolio");

        // Add action listeners to buttons
        viewStocksButton.addActionListener(e -> showStockPrices());
        buyStocksButton.addActionListener(e -> buyStocks());
        sellStocksButton.addActionListener(e -> sellStocks());
        viewPortfolioButton.addActionListener(e -> showPortfolio());

        // Panel to hold buttons
        JPanel buttonPanel = new JPanel();
        buttonPanel.setLayout(new GridLayout(1, 4));
        buttonPanel.add(viewStocksButton);
        buttonPanel.add(buyStocksButton);
        buttonPanel.add(sellStocksButton);
        buttonPanel.add(viewPortfolioButton);

        // Add components to the frame
        setLayout(new BorderLayout());
        add(buttonPanel, BorderLayout.NORTH);
        add(scrollPane, BorderLayout.CENTER);
    }

    // Initialize stock prices
    private void initializeStocks() {
        stockPrices.put("AAPL", 150.0); // Apple
        stockPrices.put("GOOGL", 2800.0); // Google
        stockPrices.put("AMZN", 3400.0); // Amazon
        stockPrices.put("TSLA", 700.0); // Tesla
        stockPrices.put("MSFT", 300.0); // Microsoft
    }

    // Display current stock prices
    private void showStockPrices() {
        displayArea.setText("=== Current Stock Prices ===\n");
        for (Map.Entry<String, Double> entry : stockPrices.entrySet()) {
            displayArea.append(entry.getKey() + " - $" + entry.getValue() + "\n");
        }
    }

    // Buy stocks
    private void buyStocks() {
        String symbol = JOptionPane.showInputDialog(this, "Enter the stock symbol (e.g., AAPL):");
        if (symbol == null || !stockPrices.containsKey(symbol)) {
            JOptionPane.showMessageDialog(this, "Invalid stock symbol.");
            return;
        }

        String quantityStr = JOptionPane.showInputDialog(this, "Enter the quantity to buy:");
        int quantity = Integer.parseInt(quantityStr);
        double totalCost = stockPrices.get(symbol) * quantity;

        if (totalCost > userBalance) {
            JOptionPane.showMessageDialog(this, "Insufficient funds.");
        } else {
            userBalance -= totalCost;
            userPortfolio.put(symbol, userPortfolio.getOrDefault(symbol, 0) + quantity);
            displayArea.setText("You bought " + quantity + " shares of " + symbol + " for $" + totalCost + "\n");
        }
    }

    // Sell stocks
    private void sellStocks() {
        String symbol = JOptionPane.showInputDialog(this, "Enter the stock symbol (e.g., AAPL):");
        if (symbol == null || !userPortfolio.containsKey(symbol)) {
            JOptionPane.showMessageDialog(this, "You don't own any shares of " + symbol);
            return;
        }

        String quantityStr = JOptionPane.showInputDialog(this, "Enter the quantity to sell:");
        int quantity = Integer.parseInt(quantityStr);
        if (quantity > userPortfolio.get(symbol)) {
            JOptionPane.showMessageDialog(this, "You don't own enough shares of " + symbol);
            return;
        }

        double totalEarnings = stockPrices.get(symbol) * quantity;
        userBalance += totalEarnings;
        userPortfolio.put(symbol, userPortfolio.get(symbol) - quantity);
        if (userPortfolio.get(symbol) == 0) {
            userPortfolio.remove(symbol);
        }
        displayArea.setText("You sold " + quantity + " shares of " + symbol + " for $" + totalEarnings + "\n");
    }

    // Display user's portfolio
    private void showPortfolio() {
        displayArea.setText("=== Your Portfolio ===\n");
        for (Map.Entry<String, Integer> entry : userPortfolio.entrySet()) {
            displayArea.append(entry.getKey() + " - " + entry.getValue() + " shares\n");
        }
        displayArea.append("Cash Balance: $" + userBalance + "\n");
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(() -> {
            new StockTrading().setVisible(true);
        });
    }
}
