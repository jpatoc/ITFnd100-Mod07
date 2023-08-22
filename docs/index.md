# Structured Error Handling + Pickling
**Dev:** *JPatoc*   
**Date:** *8.24.2023*  
**Course:** *IT FDN 110 A*  
**Assignment:** *07*


## Structured Error Handling (Try-Except)
When you are programming, you fix your bugs immediately and make sure the code runs smoothly. However, it often happens that other people introduce new bugs when they use your program.

### Raising Custom Errors
Python automatically generates errors based on conditions defined by the Python Runtime. However, you can also "raise" errors based on custom conditions (Listing 13). 

```
class InsufficientFundsError(Exception):
    def __init__(self, amount, balance):
        self.amount = amount
        self.balance = balance
        super().__init__(f"Insufficient funds: Trying to withdraw {amount} but balance is {balance}")

class InvalidBalanceError(Exception):
    def __init__(self, balance):
        self.balance = balance
        super().__init__(f"Invalid initial balance: {balance}. Initial balance must be positive.")

class BankAccount:
    def __init__(self, balance):
        if balance < 0:
            raise InvalidBalanceError(balance)
        self.balance = balance

    def deposit(self, amount):
        if amount <= 0:
            raise ValueError("Deposit amount must be greater than 0")
        self.balance += amount

    def withdraw(self, amount):
        if amount <= 0:
            raise ValueError("Withdrawal amount must be greater than 0")
        if amount > self.balance:
            raise InsufficientFundsError(amount, self.balance)
        self.balance -= amount

def get_valid_initial_balance():
    while True:
        try:
            initial_balance = float(input("Enter initial account balance: "))
            if initial_balance >= 0:
                return initial_balance
            else:
                print("Initial balance must be non-negative. Please try again.")
        except ValueError:
            print("Invalid input. Please enter a valid number.")

def main():
    initial_balance = get_valid_initial_balance()
    account = BankAccount(initial_balance)

    while True:
        try:
            print("1. Deposit\n2. Withdraw\n3. Exit")
            choice = int(input("Enter your choice: "))

            if choice == 1:
                deposit_amount = float(input("Enter deposit amount: "))
                account.deposit(deposit_amount)
                print(f"Deposit of {deposit_amount} successful. New balance: {account.balance}")
            elif choice == 2:
                withdrawal_amount = float(input("Enter withdrawal amount: "))
                account.withdraw(withdrawal_amount)
                print(f"Withdrawal of {withdrawal_amount} successful. New balance: {account.balance}")
            elif choice == 3:
                print("Exiting the program.")
                break
            else:
                print("Invalid choice. Please select a valid option.")

        except ValueError as ve:
            print(f"Error: {ve}. Please try again.")
        except InsufficientFundsError as ife:
            print(f"Error: {ife}. Please try again.")

if __name__ == "__main__":
    main()
```
## Pickling - Object Serialization/De-serialization
Pickling is a process in Python that involves serializing and deserializing Python objects, converting them into a byte stream that can be stored or transmitted. This enables data to be saved in a format that preserves its structure and state, allowing objects to be reconstructed later, making it useful for tasks such as data persistence and inter-process communication.

```
import pickle


def load_data_from_file(filename):
    with open(filename, "r") as file:
        lines = file.readlines()

    records = []
    for line in lines:
        cleaned_line = line.strip()
        if cleaned_line:
            parts = cleaned_line.split(",")
            if len(parts) == 2:
                first_name, age = parts
                last_name = ""  # If last name is not present in the data file
                record = {
                    "first_name": first_name,
                    "last_name": last_name,
                    "age": int(age)
                }
                records.append(record)
    return records


def print_records(records):
    print("Records:")
    for index, record in enumerate(records, start=1):
        print(f"{index}. Name: {record['first_name']} {record['last_name']}, Age: {record['age']}")


def save_data_to_pickle(data, pickle_filename):
    with open(pickle_filename, "wb") as pickle_file:
        pickle.dump(data, pickle_file)


def main():
    text_filename = "data.txt"
    pickle_filename = "data.pkl"

    data = load_data_from_file(text_filename)
    print_records(data)

    save_choice = input("Do you want to save this data to a pickle file? (yes/no): ")
    if save_choice.lower() == "yes":
        save_data_to_pickle(data, pickle_filename)
        print("Data saved to pickle file.")
    else:
        print("Data not saved.")


if __name__ == "__main__":
    main()
```

