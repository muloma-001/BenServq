Library Management System

A simple Python-based Library Management System that allows users to manage books and members efficiently.  
The system supports adding, updating, searching, borrowing, and returning books, as well as registering and managing library members.

 Features

- Add new books and members  
- Search for books by title, author, or genre  
- Update and delete existing book records  
- Borrow and return books  
- Track borrowed books for each member  
- Validate book genres and prevent duplicate ISBNs  

 Class Structure
 LibraryManagementSystem
*Attributes*
- books: dict — Stores book data by ISBN  
- members: list — Stores registered library members  
- valid_genres: tuple[str] — Accepted book genres  
- next_member_id: int — Auto-incremented ID for new members  

*Methods*
- add_book(isbn, title, author, genre, total_copies)  
- add_member(name, email)  
- search_books(keyword)  
- update_book(isbn, title=None, author=None, genre=None)  
- delete_book(isbn)  
- borrow_book(member_id, isbn)  
- return_book(member_id, isbn)

 Book
*Attributes*
- isbn: str — Unique book identifier  
- title: str  
- author: str  
- genre: str  
- total_copies: int  
- available_copies: int  
 Member
*Attributes*
- member_id: str — Unique member ID  
- name: str  
- email: str  
- borrowed_books: list — List of borrowed book ISBNs  

⚙️ How It Works

1. The librarian (user) can add books and register members.
2. Members can borrow books if available.
3. Each borrowed book reduces the available copies count.
4. Returning a book increases the available copies.
5. The system maintains a record of borrowed books for each member.

Example Usage

```python
from library_management import LibraryManagementSystem

 Initialize system
library = LibraryManagementSystem()

 Add books
library.add_book("9780135166307", "Clean Code", "Robert C. Martin", "Programming", 5)
library.add_book("9781491957660", "Fluent Python", "Luciano Ramalho", "Programming", 3)

 Register members
library.add_member("Alice Johnson", "alice@example.com")
library.add_member("Bob Smith", "bob@example.com")

 Search and borrow
library.search_books("Python")
library.borrow_book(1, "9781491957660")
library.return_book(1, "9781491957660")
