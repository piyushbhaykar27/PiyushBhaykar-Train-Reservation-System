# PiyushBhaykar-Train-Reservation-System
# Importing required libraries
import sqlite3
from getpass import getpass

# Database connection
conn = sqlite3.connect('train_reservation.db')
c = conn.cursor()

# Create tables if they don't exist
c.execute('''CREATE TABLE IF NOT EXISTS users
             (username TEXT PRIMARY KEY, password TEXT)''')
c.execute('''CREATE TABLE IF NOT EXISTS trains
             (train_id INTEGER PRIMARY KEY, train_name TEXT, source TEXT, destination TEXT, departure_time TEXT, arrival_time TEXT)''')
c.execute('''CREATE TABLE IF NOT EXISTS reservations
             (reservation_id INTEGER PRIMARY KEY, username TEXT, train_id INTEGER, booking_date TEXT, FOREIGN KEY(username) REFERENCES users(username), FOREIGN KEY(train_id) REFERENCES trains(train_id))''')

# User Authentication System
def register_user():
    username = input("Enter a username: ")
    password = input("Enter a password: ")
    c.execute("INSERT INTO users VALUES (?, ?)", (username, password))
    conn.commit()
    print("User registered successfully!")

def login_user():
    username = input("Enter your username: ")
    password = input("Enter your password: ")
    c.execute("SELECT * FROM users WHERE username=? AND password=?", (username, password))
    if c.fetchone():
        print("Login successful!")
        return username
    else:
        print("Invalid username or password!")
        return None

# Train Schedule Display
def display_trains():
    c.execute("SELECT * FROM trains")
    trains = c.fetchall()
    for train in trains:
        print(f"Train ID: {train[0]}, Train Name: {train[1]}, Source: {train[2]}, Destination: {train[3]}, Departure Time: {train[4]}, Arrival Time: {train[5]}")

# Booking Module
def book_ticket(username):
    train_id = int(input("Enter the train ID: "))
    c.execute("SELECT * FROM trains WHERE train_id=?", (train_id,))
    train = c.fetchone()
    if train:
        c.execute("INSERT INTO reservations VALUES (NULL, ?, ?, DATE('now'))", (username, train_id))
        conn.commit()
        print("Ticket booked successfully!")
    else:
        print("Invalid train ID!")

# Reservation Management
def view_reservations(username):
    c.execute("SELECT * FROM reservations WHERE username=?", (username,))
    reservations = c.fetchall()
    for reservation in reservations:
        print(f"Reservation ID: {reservation[0]}, Train ID: {reservation[2]}, Booking Date: {reservation[3]}")

# Main function
def main():
    while True:
        print("1. Register")
        print("2. Login")
        print("3. Exit")
        choice = input("Enter your choice: ")
        if choice == "1":
            register_user()
        elif choice == "2":
            username = login_user()
            if username:
                while True:
                    print("1. Display Trains")
                    print("2. Book Ticket")
                    print("3. View Reservations")
                    print("4. Logout")
                    choice = input("Enter your choice: ")
                    if choice == "1":
                        display_trains()
                    elif choice == "2":
                        book_ticket(username)
                    elif choice == "3":
                        view_reservations(username)
                    elif choice == "4":
                        break
                    else:
                        print("Invalid choice!")
        elif choice == "3":
            break
        else:
            print("Invalid choice!")

if __name__ == "__main__":
    main()
