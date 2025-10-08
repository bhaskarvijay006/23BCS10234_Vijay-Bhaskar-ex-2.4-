# 23BCS10234_Vijay-Bhaskar-ex-2.4

import java.sql.*;
import java.util.*;

// ---------- MODEL CLASSES ----------
class Student {
    private int id;
    private String name;
    private int age;
    private String course;

    public Student(int id, String name, int age, String course) {
        this.id = id;
        this.name = name;
        this.age = age;
        this.course = course;
    }

    public int getId() { return id; }
    public String getName() { return name; }
    public int getAge() { return age; }
    public String getCourse() { return course; }
}

class Product {
    private int id;
    private String name;
    private double price;

    public Product(int id, String name, double price) {
        this.id = id;
        this.name = name;
        this.price = price;
    }

    public int getId() { return id; }
    public String getName() { return name; }
    public double getPrice() { return price; }
}

// ---------- DAO CLASSES ----------
class DatabaseConnection {
    static final String URL = "jdbc:mysql://localhost:3306/college_db";
    static final String USER = "root";
    static final String PASS = "your_mysql_password";

    public static Connection getConnection() throws SQLException {
        return DriverManager.getConnection(URL, USER, PASS);
    }
}

class StudentDAO {
    public List<Student> getAllStudents() throws Exception {
        List<Student> list = new ArrayList<>();
        Connection con = DatabaseConnection.getConnection();
        Statement st = con.createStatement();
        ResultSet rs = st.executeQuery("SELECT * FROM students");
        while (rs.next()) {
            list.add(new Student(rs.getInt("id"), rs.getString("name"), rs.getInt("age"), rs.getString("course")));
        }
        con.close();
        return list;
    }
}

class ProductDAO {
    Connection con = DatabaseConnection.getConnection();
    Statement stmt = con.createStatement();

    public ProductDAO() throws Exception {}

    public void insertProduct(String name, double price) throws Exception {
        stmt.executeUpdate("INSERT INTO products(name, price) VALUES ('" + name + "', " + price + ")");
        System.out.println(" Product inserted successfully!");
    }

    public void updateProduct(int id, double price) throws Exception {
        stmt.executeUpdate("UPDATE products SET price=" + price + " WHERE id=" + id);
        System.out.println(" Product updated successfully!");
    }

    public void deleteProduct(int id) throws Exception {
        stmt.executeUpdate("DELETE FROM products WHERE id=" + id);
        System.out.println(" Product deleted successfully!");
    }

    public void displayProducts() throws Exception {
        ResultSet rs = stmt.executeQuery("SELECT * FROM products");
        System.out.println("\n Product Table:");
        while (rs.next()) {
            System.out.println(rs.getInt("id") + " " + rs.getString("name") + " " + rs.getDouble("price"));
        }
    }
}

// ---------- CONTROLLER ----------
public class StudentProductApp {
    public static void main(String[] args) {
        try {
            Class.forName("com.mysql.cj.jdbc.Driver");
            Scanner sc = new Scanner(System.in);
            ProductDAO productDAO = new ProductDAO();
            StudentDAO studentDAO = new StudentDAO();

            while (true) {
                System.out.println("\n===== JDBC APPLICATION MENU =====");
                System.out.println("1. Show Students (Fetch Data)");
                System.out.println("2. Insert Product");
                System.out.println("3. Update Product");
                System.out.println("4. Delete Product");
                System.out.println("5. Display Products");
                System.out.println("6. Exit");
                System.out.print("Enter your choice: ");
                int choice = sc.nextInt();

                switch (choice) {
                    case 1:
                        System.out.println("\n Student Table Data:");
                        for (Student s : studentDAO.getAllStudents()) {
                            System.out.println(s.getId() + " " + s.getName() + " " + s.getAge() + " " + s.getCourse());
                        }
                        break;

                    case 2:
                        System.out.print("Enter Product Name: ");
                        String name = sc.next();
                        System.out.print("Enter Product Price: ");
                        double price = sc.nextDouble();
                        productDAO.insertProduct(name, price);
                        break;

                    case 3:
                        System.out.print("Enter Product ID to Update: ");
                        int idu = sc.nextInt();
                        System.out.print("Enter New Price: ");
                        double newPrice = sc.nextDouble();
                        productDAO.updateProduct(idu, newPrice);
                        break;

                    case 4:
                        System.out.print("Enter Product ID to Delete: ");
                        int idd = sc.nextInt();
                        productDAO.deleteProduct(idd);
                        break;

                    case 5:
                        productDAO.displayProducts();
                        break;

                    case 6:
                        System.out.println("Exiting... ");
                        sc.close();
                        System.exit(0);
                        break;

                    default:
                        System.out.println(" Invalid Choice!");
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}


//Database Setup

CREATE DATABASE college_db;
USE college_db;

CREATE TABLE students (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(50),
    age INT,
    course VARCHAR(50)
);

INSERT INTO students (name, age, course)
VALUES ('Ravi', 21, 'B.Tech'), ('Priya', 22, 'MBA'), ('Karan', 20, 'BCA');

CREATE TABLE products (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(50),
    price DOUBLE
);
