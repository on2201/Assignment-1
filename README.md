#include <iostream>
#include <vector>
#include <string>
#include <algorithm>
using namespace std;

class Book {
    string title, author, isbn;
    bool available; 
public:
    Book(string t, string a, string i)
        : title(t), author(a), isbn(i), available(true) {} 

    string getTitle() const { return title; }
    string getAuthor() const { return author; }
    string getISBN() const { return isbn; }
    bool isAvailable() const { return available; }

    void borrow() {
        if (available) {
            available = false;
            cout << "Book borrowed: " << title << endl;
        } else {
            cout << "Book is already borrowed!\n";
        }
    }

    void ret() {
        if (!available) {
            available = true;
            cout << "Book returned: " << title << endl;
        } else {
            cout << "Book was not borrowed!" << endl;
        }
    }

    void show() const {
        cout << "Title: " << title
             << ", Author: " << author
             << ", ISBN: " << isbn
             << ", " << (available ? "Available" : "Borrowed") << endl;
    }
};

class User {
    string name;
    vector<Book*> borrowedBooks;
public:
    User(string n) : name(n) {}

    string getName() const { return name; }

    void borrowBook(Book* book) {
        if (book && book->isAvailable()) {
            book->borrow();
            borrowedBooks.push_back(book);
        } else if (book) {
            cout << "Sorry " << name << ", book not available!" << endl;
        }
    }

    void returnBook(Book* book) {

        vector<Book*>::iterator it = find(borrowedBooks.begin(), borrowedBooks.end(), book);
        if (it != borrowedBooks.end()) {
            book->ret();
            borrowedBooks.erase(it);
        } else {
            cout << name << " didn't borrow that book!" << endl;
        }
    }

    void showBorrowedBooks() const {
        cout << name << " borrowed books:\n";
        if (borrowedBooks.empty()) {
            cout << "None\n";
        } else {
            for (size_t i = 0; i < borrowedBooks.size(); ++i) {
                borrowedBooks[i]->show();
            }
        }
        cout << endl;
    }
};

class Library {
    vector<Book*> books;
public:
    ~Library() { for (size_t i = 0; i < books.size(); ++i) delete books[i]; }

    void addBook(Book* book) {
        books.push_back(book);
        cout << "Book added: " << book->getTitle() << endl;
    }

    void removeBookByISBN(const string& isbn) {
        vector<Book*>::iterator it = books.begin();
        bool removed = false;
        while (it != books.end()) {
            if ((*it)->getISBN() == isbn) {
                delete *it;
                it = books.erase(it);
                removed = true;
            } else {
                ++it;
            }
        }
        if (removed) {
            cout << "Book removed with ISBN: " << isbn << endl;
        } else {
            cout << "Book not found!" << endl;
        }
    }

    Book* searchBookByTitle(const string& title) const {
        for (size_t i = 0; i < books.size(); ++i) {
            if (books[i]->getTitle() == title) return books[i];
        }
        cout << "Book not found!" << endl;
        return NULL;
    }

    void showAllBooks() const {
        cout << "Library Books:\n";
        for (size_t i = 0; i < books.size(); ++i) books[i]->show();
        cout << endl;
    }
};

int main() {
    Library lib;
    User salat("Salat");
    User abdi("Abdi");

    lib.addBook(new Book("C++ Basics", "Stroustrup", "456"));
    lib.addBook(new Book("OOP Concepts", "Gosling", "265"));
    lib.addBook(new Book("Data Structures", "Knuth", "245"));

    lib.showAllBooks();

    Book* b1 = lib.searchBookByTitle("C++ Basics");
    salat.borrowBook(b1); 
    abdi.borrowBook(b1);   

    salat.showBorrowedBooks();
    salat.returnBook(b1); 
    abdi.borrowBook(b1);   

    abdi.showBorrowedBooks();
    lib.removeBookByISBN("265"); 
    
    lib.showAllBooks();

}
