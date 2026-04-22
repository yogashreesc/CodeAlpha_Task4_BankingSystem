# CodeAlpha_Task4_BankingSystem
C++ banking system implementing account management, deposits, withdrawals and balance enquiry using classes and file handling.


#include <iostream>
#include <fstream>
using namespace std;

class BankAccount {
private:
    int accNo;
    char name[50];
    float balance;

public:
    void createAccount() {
        cout << "Enter Account Number: ";
        cin >> accNo;
        cout << "Enter Name: ";
        cin.ignore();
        cin.getline(name, 50);
        cout << "Enter Initial Balance: ";
        cin >> balance;
    }

    void showAccount() {
        cout << "\nAccount No: " << accNo;
        cout << "\nName: " << name;
        cout << "\nBalance: " << balance << endl;
    }

    int getAccNo() {
        return accNo;
    }

    void deposit(float amount) {
        balance += amount;
    }

    void withdraw(float amount) {
        if(amount <= balance)
            balance -= amount;
        else
            cout << "Insufficient Balance!\n";
    }

    float getBalance() {
        return balance;
    }
};

void writeAccount() {
    BankAccount acc;
    ofstream outFile("accounts.dat", ios::binary | ios::app);

    acc.createAccount();
    outFile.write((char*)&acc, sizeof(acc));

    outFile.close();
    cout << "Account Created Successfully!\n";
}

void displayAccount(int accNo) {
    BankAccount acc;
    ifstream inFile("accounts.dat", ios::binary);

    bool found = false;

    while(inFile.read((char*)&acc, sizeof(acc))) {
        if(acc.getAccNo() == accNo) {
            acc.showAccount();
            found = true;
        }
    }

    inFile.close();

    if(!found)
        cout << "Account not found!\n";
}


void modifyAccount(int accNo, int option) {
    BankAccount acc;
    fstream file("accounts.dat", ios::binary | ios::in | ios::out);

    bool found = false;

    while(file.read((char*)&acc, sizeof(acc))) {
        if(acc.getAccNo() == accNo) {
            acc.showAccount();

            float amount;
            cout << "\nEnter amount: ";
            cin >> amount;

            if(option == 1)
                acc.deposit(amount);
            else if(option == 2)
                acc.withdraw(amount);

            int pos = -1 * sizeof(acc);
            file.seekp(pos, ios::cur);
            file.write((char*)&acc, sizeof(acc));

            cout << "Transaction Successful!\n";
            found = true;
            break;
        }
    }

    file.close();

    if(!found)
        cout << "Account not found!\n";
}

// Menu
int main() {
    int choice, accNo;

    do {
        cout << "\n===== BANK MENU =====\n";
        cout << "1. Create Account\n";
        cout << "2. Deposit\n";
        cout << "3. Withdraw\n";
        cout << "4. Balance Enquiry\n";
        cout << "5. Exit\n";
        cout << "Enter choice: ";
        cin >> choice;

        switch(choice) {
            case 1:
                writeAccount();
                break;

            case 2:
                cout << "Enter Account Number: ";
                cin >> accNo;
                modifyAccount(accNo, 1);
                break;

            case 3:
                cout << "Enter Account Number: ";
                cin >> accNo;
                modifyAccount(accNo, 2);
                break;

            case 4:
                cout << "Enter Account Number: ";
                cin >> accNo;
                displayAccount(accNo);
                break;

            case 5:
                cout << "Thank you!\n";
                break;

            default:
                cout << "Invalid choice!\n";
        }

    } while(choice != 5);

    return 0;
}
