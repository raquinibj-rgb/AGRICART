import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.Map;

// 1. PRODUCT CLASS PARA SA AGRIMATERIALS
class AgriProduct {
    String name;
    double price;
    int stock;

    public AgriProduct(String name, double price, int stock) {
        this.name = name;
        this.price = price;
        this.stock = stock;
    }
}

// 2. MAIN APPLICATION CLASS
public class AgriMaterialsApp extends JFrame {
    private Map<String, AgriProduct> inventory = new HashMap<>();
    private DefaultListModel<String> cartModel = new DefaultListModel<>();
    private JList<String> cartList = new JList<>(cartModel);
    private JLabel totalLabel = new JLabel("Total: ₱0.00");
    private double totalAmount = 0;

    public AgriMaterialsApp() {
        // Setup ng mga Agricultural Materials (Initial Stock)
        inventory.put("Organic Fertilizer", new AgriProduct("Organic Fertilizer", 550.0, 10));
        inventory.put("Hybrid Rice Seeds", new AgriProduct("Hybrid Rice Seeds", 1200.0, 15));
        inventory.put("Hand Tractor Oil", new AgriProduct("Hand Tractor Oil", 350.0, 8));
        inventory.put("Pesticide Spray", new AgriProduct("Pesticide Spray", 450.0, 12));

        setTitle("Agri-Cart Digital Solution");
        setSize(700, 500);
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setLayout(new BorderLayout(15, 15));

        // --- Left Panel: Available Materials (Store) ---
        JPanel storePanel = new JPanel();
        storePanel.setLayout(new BoxLayout(storePanel, BoxLayout.Y_AXIS));
        storePanel.setBorder(BorderFactory.createTitledBorder("Available Materials"));

        for (AgriProduct p : inventory.values()) {
            JPanel itemPanel = new JPanel(new FlowLayout(FlowLayout.LEFT));
            JLabel label = new JLabel(String.format("%s (₱%.2f) | Stock: %d", p.name, p.price, p.stock));
            JButton addBtn = new JButton("Add to Cart");

            // Event Handling para sa pag-add ng item
            addBtn.addActionListener(e -> {
                if (p.stock > 0) {
                    p.stock--; // Bawasan ang stock sa inventory
                    label.setText(String.format("%s (₱%.2f) | Stock: %d", p.name, p.price, p.stock));
                    
                    cartModel.addElement(p.name + " - ₱" + p.price);
                    totalAmount += p.price;
                    totalLabel.setText("Total: ₱" + String.format("%.2f", totalAmount));
                } else {
                    JOptionPane.showMessageDialog(this, p.name + " is out of stock!");
                }
            });
            
            itemPanel.add(label);
            itemPanel.add(addBtn);
            storePanel.add(itemPanel);
        }

        // --- Right Panel: Shopping Cart Display ---
        JPanel cartPanel = new JPanel(new BorderLayout());
        cartPanel.setBorder(BorderFactory.createTitledBorder("Your Shopping Cart"));
        cartPanel.add(new JScrollPane(cartList), BorderLayout.CENTER);

        // --- Footer: Checkout Section ---
        JPanel footer = new JPanel(new FlowLayout(FlowLayout.RIGHT));
        JButton checkoutBtn = new JButton("Complete Purchase");
        checkoutBtn.setBackground(new Color(34, 139, 34)); // Forest Green
        checkoutBtn.setForeground(Color.WHITE);
        checkoutBtn.setFont(new Font("Arial", Font.BOLD, 12));

        checkoutBtn.addActionListener(e -> processCheckout());

        footer.add(totalLabel);
        footer.add(checkoutBtn);
        cartPanel.add(footer, BorderLayout.SOUTH);

        // Layout Assembly
        add(new JScrollPane(storePanel), BorderLayout.WEST);
        add(cartPanel, BorderLayout.CENTER);
    }

    private void processCheckout() {
        if (cartModel.isEmpty()) {
            JOptionPane.showMessageDialog(this, "Your cart is empty!");
            return;
        }

        // GUI for User Information (Gaya ng sa video [00:02:45])
        String name = JOptionPane.showInputDialog(this, "Enter Customer Name:");
        if (name != null && !name.trim().isEmpty()) {
            JOptionPane.showMessageDialog(this, 
                "TRANSACTION SUCCESSFUL\n" +
                "Customer: " + name + "\n" +
                "Total Paid: ₱" + String.format("%.2f", totalAmount) + "\n\n" +
                "Thank you for supporting our local farmers!");
            
            // Reset Cart after purchase
            cartModel.clear();
            totalAmount = 0;
            totalLabel.setText("Total: ₱0.00");
        }
    }

    public static void main(String[] args) {
        // Run the GUI on the Event Dispatch Thread
        SwingUtilities.invokeLater(() -> new AgriMaterialsApp().setVisible(true));
    }
}
