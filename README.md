# Simple Bank Account System
# Classes:User, Admin
import os

def clearTerminal():
    os.system('cls' if os.name == 'nt' else 'clear')

class User:
    user_type = "User"
    def __init__(self, name, user_id, email):
        self.name = name
        self.user_id = user_id
        self.email = email 
        self.balance = 0
        self.banned = False

    def __str__(self):
        return f"Username: {self.name}\nID: {self.user_id}\nEmail: {self.email}"

    def check_balance(self):
        if not self.banned:
            return f"Account Balance: ${self.balance}"
        else:
            return f"You are banned from using our banking system, please contact an Admin."

    def deposit(self, amount):
        if not self.banned:
            try:
                amount = int(amount)
                self.balance = self.balance + amount
            except:
                return f"Transaction failed: An error has occured."
            finally:
                return f"Successfully deposited ${amount}. {self.check_balance()}."
        else:
            return f"You are banned from using our banking system, please contact an Admin."

    def withdraw(self, amount):
        if not self.banned:
            amount = int(amount)
            if self.balance >= amount:
                try:
                    self.balance = self.balance - amount
                except:
                    return f"Transaction failed: An error has occured."
                finally:
                    return f"Successfully withdrew ${amount}. {self.check_balance()}."
            else:
                return f"Amount does not exist in account. {self.check_balance()}"
        else:
            return f"You are banned from using our banking system, please contact an Admin."


class Admin(User):
    user_type = "Admin"
    def __init__(self, name, user_id, email):
        super().__init__(name, user_id, email)
        self.permissions = ["toggle_ban_user", "create_user"]

    def __str__(self):
        return f"Admin name: {self.name}\nAdmin ID: {self.user_id}\nPermissions: {self.permissions}"

    def toggle_ban_user(self, user):
        if "toggle_ban_user" in self.permissions:
            if user.banned:
                user.banned = False
                return f"User {user.name} unbanned successfully." 
            else:
                user.banned = True
                return f"User {user.name} banned successfully."
        else:
            return f"You do not have the permissions to perform this action."

    def create_new_user(self, username, user_password, user_id, user_email):
        if "create_user" in self.permissions:
            if username in database:
                print("Cannot create a new user with this username since it already exists.")
            else:
                database.update({ username: { "profile": User(username, user_id, user_email), "password": user_password } })
                print(f"New user created successfully: {database.get(username)}")


database = {
    "Admin": {
        "profile": Admin("Admin", 1, "admin@email.com"),
        "password": "1234"
    },

    "John_Doe": {
        "profile": User("John Doe", 15, "johndoe@email.com"),
        "password": "johndoe"
    },
}

# Program loop
def main(user_profile):
    while True:
        user_input = input("> ").split(" ")
        user_type = user_profile.user_type

        match user_input[0].lower():
            case "help":
                print("Commands:\nexit\nlogout\nbalance\ndeposit\nwithdraw") 
                if user_type == "Admin":
                    print("toggle_ban\ncreate_new_user")
            case "balance":
                print(user_profile.check_balance())
            case "deposit":
                print(user_profile.deposit(user_input[1]))
            case "withdraw":
                print(user_profile.withdraw(user_input[1]))
            case "toggle_ban":
                if user_type != "Admin":
                    print("You do not have the permissions to perform this action.")
                else:
                    print(user_profile.toggle_ban_user(database.get(user_input[1]).get("profile")))
            case "create_new_user":
                if user_type != "Admin":
                    print("You do not have the permissions to perform this action.")
                else:
                    print(user_profile.create_new_user(user_input[1], user_input[2], user_input[3], user_input[4]))
            case "exit":
                print("Have a nice day!")
                return
            case "logout":
                login()

            case _:
                print("Error: Command does NOT exist.") 

# Program start
def login():
    clearTerminal()
    user_profile = None

    if not user_profile:
        username_login = input("Enter username: ")
        if username_login in database:
            username_login = database.get(username_login)
            password_login = input("Enter password: ")

            if password_login in username_login.values():
                user_profile = username_login.get("profile")
                clearTerminal()
                print("Logged in successfully!\n")
                print('Type "help" for commands\n')
                main(user_profile)

login()
