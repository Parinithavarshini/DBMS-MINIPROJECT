# DBMS-MINIPROJECT
RESTAURANT MANAGEMENT SYSTEM
import mysql.connector
from mysql.connector import Error

def create_connection():
    try:
        connection = mysql.connector.connect(
            host='127.0.0.1',
            database='restaurant',
            user='root',
            password='15-May-05'
        )
        if connection.is_connected():
            print("Connected to MySQL database")
            return connection
    except Error as e:
        print("Error while connecting to MySQL", e)
        return None

def create_employee_table():
    try:
        connection = create_connection()
        if connection is not None:
            cursor = connection.cursor()
            cursor.execute('''
                CREATE TABLE IF NOT EXISTS employee (
                    emp_id INT AUTO_INCREMENT PRIMARY KEY,
                    name VARCHAR(100) NOT NULL,
                    age INT NOT NULL,
                    contact VARCHAR(15) NOT NULL,
                    designation VARCHAR(50) NOT NULL,
                    salary DECIMAL(10, 2) NOT NULL
                )
            ''')
            connection.commit()
            print("Employee table created.")
    except Error as e:
        print("Failed to create employee table:", e)
    finally:
        if connection.is_connected():
            cursor.close()
            connection.close()

def add_employee(emp_id, name, age, contact, designation, salary):
    connection = create_connection()
    if connection is not None:
        cursor = connection.cursor()
        cursor.execute('INSERT INTO employee (emp_id, name, age, contact, designation, salary) VALUES (%s, %s, %s, %s, %s, %s)',
                       (emp_id, name, age, contact, designation, salary))
        connection.commit()
        cursor.close()
        connection.close()
        print("Employee added.")

def delete_employee(emp_id):
    connection = create_connection()
    if connection is not None:
        cursor = connection.cursor()
        cursor.execute('DELETE FROM employee WHERE emp_id = %s', (emp_id,))
        connection.commit()
        cursor.close()
        connection.close()
        print("Employee deleted.")

def add_menu_item(name, price):
    connection = create_connection()
    if connection is not None:
        cursor = connection.cursor()
        cursor.execute('INSERT INTO menu_items (name, price) VALUES (%s, %s)', (name, price))
        connection.commit()
        cursor.close()
        connection.close()
        print("Menu item added.")

def view_menu():
    connection = create_connection()
    if connection is not None:
        cursor = connection.cursor()
        cursor.execute('SELECT * FROM menu_items')
        items = cursor.fetchall()
        cursor.close()
        connection.close()
        return items

def update_menu_item(item_id, name, price):
    connection = create_connection()
    if connection is not None:
        cursor = connection.cursor()
        cursor.execute('UPDATE menu_items SET name = %s, price = %s WHERE id = %s', (name, price, item_id))
        connection.commit()
        cursor.close()
        connection.close()
        print("Menu item updated.")

def delete_menu_item(item_id):
    connection = create_connection()
    if connection is not None:
        cursor = connection.cursor()
        cursor.execute('DELETE FROM menu_items WHERE id = %s', (item_id,))
        connection.commit()
        cursor.close()
        connection.close()
        print("Menu item deleted.")

def place_order(item_id, quantity, customer_id):
    connection = create_connection()
    if connection is not None:
        cursor = connection.cursor()
        cursor.execute('SELECT price FROM menu_items WHERE id = %s', (item_id,))
        price = cursor.fetchone()[0]
        total_price = price * quantity
        cursor.execute('INSERT INTO orders (item_id, quantity, total_price, customer_id) VALUES (%s, %s, %s, %s)',
                       (item_id, quantity, total_price, customer_id))
        connection.commit()
        cursor.close()
        connection.close()
        print("Order placed.")

def view_orders():
    connection = create_connection()
    if connection is not None:
        cursor = connection.cursor()
        cursor.execute('SELECT orders.id, menu_items.name, orders.quantity, orders.total_price FROM orders JOIN menu_items ON orders.item_id = menu_items.id')
        orders = cursor.fetchall()
        cursor.close()
        connection.close()
        return orders

def customer_view_orders(customer_id):
    connection = create_connection()
    if connection is not None:
        cursor = connection.cursor()
        cursor.execute('SELECT orders.id, menu_items.name, orders.quantity, orders.total_price FROM orders JOIN menu_items ON orders.item_id = menu_items.id WHERE orders.customer_id = %s',
                       (customer_id,))
        orders = cursor.fetchall()
        cursor.close()
        connection.close()
        return orders

def customer_main(customer_id):
    while True:
        print("\nCustomer Menu")
        print("1. View Menu")
        print("2. View My Orders")
        print("3. Exit")

        choice = input("Enter your choice: ")

        if choice == '1':
            menu = view_menu()
            for item in menu:
                print(f"{item[0]}. {item[1]} - Rs.{item[2]:.2f}")
        elif choice == '2':
            orders = customer_view_orders(customer_id)
            for order in orders:
                print(f"Order ID: {order[0]}, Item: {order[1]}, Quantity: {order[2]}, Total Price: Rs.{order[3]:.2f}")
        elif choice == '3':
            print("Exiting...")
            break
        else:
            print("Invalid choice. Please try again.")

def main():
    while True:
        print("\nRestaurant Management System")
        print("1. Add Menu Item")
        print("2. View Menu")
        print("3. Update Menu Item")
        print("4. Delete Menu Item")
        print("5. Place Order")
        print("6. View Orders")
        print("7. Create Employee Table")
        print("8. Add Employee")
        print("9. Delete Employee")
        print("10. Customer Menu")
        print("11. Exit")
       
        choice = input("Enter your choice: ")

        if choice == '1':
            name = input("Enter item name: ")
            price = float(input("Enter item price: "))
            add_menu_item(name, price)
        elif choice == '2':
            menu = view_menu()
            for item in menu:
                print(f"{item[0]}. {item[1]} - Rs.{item[2]:.2f}")
        elif choice == '3':
            item_id = int(input("Enter item ID to update: "))
            name = input("Enter new item name: ")
            price = float(input("Enter new item price: "))
            update_menu_item(item_id, name, price)
        elif choice == '4':
            item_id = int(input("Enter item ID to delete: "))
            delete_menu_item(item_id)
        elif choice == '5':
            item_id = int(input("Enter item ID to order: "))
            quantity = int(input("Enter quantity: "))
            customer_id = int(input("Enter your customer ID: "))
            place_order(item_id, quantity, customer_id)
        elif choice == '6':
            orders = view_orders()
            for order in orders:
                print(f"Order ID: {order[0]}, Item: {order[1]}, Quantity: {order[2]}, Total Price: Rs.{order[3]:.2f}")
        elif choice == '7':
            create_employee_table()
        elif choice == '8':
            emp_id = int(input("Enter employee ID: "))
            name = input("Enter employee name: ")
            age = int(input("Enter employee age: "))
            contact = input("Enter employee contact: ")
            designation = input("Enter employee designation: ")
            salary = float(input("Enter employee salary: "))
            add_employee(emp_id, name, age, contact, designation, salary)
        elif choice == '9':
            emp_id = int(input("Enter employee ID to delete: "))
            delete_employee(emp_id)
        elif choice == '10':
            customer_id = int(input("Enter your customer ID: "))
            customer_main(customer_id)
        elif choice == '11':
            print("Exiting...")
            break
        else:
            print("Invalid choice. Please try again.")

if __name__ == '__main__':
    main()

