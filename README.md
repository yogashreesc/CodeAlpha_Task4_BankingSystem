#include <iostream>
#include <fstream>
using namespace std;

class Account {
public:
    int accNo;
    char name[50];
    float balance;

    void create() {
        cout << "Enter Account No: ";
        cin >> accNo;
        cout << "Enter Name: ";
        cin >> name;
        cout << "Enter Balance: ";
        cin >> balance;
    }

    void display() {
        cout << "\nAccount No: " << accNo;
        cout << "\nName: " << name;
        cout << "\nBalance: " << balance << endl;
    }
};
void addAccount() {
    Account a;
    ofstream file("bank.dat", ios::binary | ios::app);

    a.create();
    file.write((char*)&a, sizeof(a));

    file.close();
    cout << "Account Created!\n";
}
void showAccount(int num) {
    Account a;
    ifstream file("bank.dat", ios::binary);

    while(file.read((char*)&a, sizeof(a))) {
        if(a.accNo == num) {
            a.display();
            return;
        }
    }

    cout << "Account not found!\n";
}
void updateAccount(int num, int type) {
    Account a;
    fstream file("bank.dat", ios::binary | ios::in | ios::out);

    while(file.read((char*)&a, sizeof(a))) {
        if(a.accNo == num) {
            float amt;
            cout << "Enter amount: ";
            cin >> amt;

            if(type == 1)
                a.balance += amt;
            else if(type == 2 && amt <= a.balance)
                a.balance -= amt;
            else
                cout << "Insufficient balance!\n";

            int pos = -1 * sizeof(a);
            file.seekp(pos, ios::cur);
            file.write((char*)&a, sizeof(a));

            cout << "Updated successfully!\n";
            return;
        }
    }

    cout << "Account not found!\n";
}

int main() {
    int ch, num;

    do {
        cout << "\n--- BANK MENU ---\n";
        cout << "1. Create Account\n";
        cout << "2. Deposit\n";
        cout << "3. Withdraw\n";
        cout << "4. Balance Enquiry\n";
        cout << "5. Exit\n";
        cout << "Enter choice: ";
        cin >> ch;

        switch(ch) {
            case 1:
                addAccount();
                break;

            case 2:
                cout << "Enter Account No: ";
                cin >> num;
                updateAccount(num, 1);
                break;

            case 3:
                cout << "Enter Account No: ";
                cin >> num;
                updateAccount(num, 2);
                break;

            case 4:
                cout << "Enter Account No: ";
                cin >> num;
                showAccount(num);
                break;

            case 5:
                cout << "Thank you!\n";
                break;

            default:
                cout << "Invalid choice!\n";
        }

    } while(ch != 5);

    return 0;
}