class LibraryManagementSystem:
    def __init__(self):
        # Dictionary for books: ISBN -> book details
        self.books = {}

        # List of dictionaries for members
        self.members = []

        # Tuple of valid genres
        self.valid_genres = ("Fiction", "Non-Fiction", "Sci-Fi", "Mystery", "Romance", "Biography", "History")

        # Counter for generating unique member IDs
        self.next_member_id = 1

    def add_book(self, isbn, title, author, genre, total_copies):
        """Add a book if ISBN is unique and genre is valid"""
        if not isbn or not title or not author or not genre:
            return False, "Error: All fields are required!"

        # Check if ISBN already exists
        if isbn in self.books:
            return False, f"Error: Book with ISBN '{isbn}' already exists!"

        # Validate genre
        if genre not in self.valid_genres:
            return False, f"Error: Invalid genre! Must be one of: {self.valid_genres}"

        # Validate total copies
        if total_copies < 0:
            return False, "Error: Total copies cannot be negative!"

        # Add book to dictionary
        self.books[isbn] = {
            'title': title,
            'author': author,
            'genre': genre,
            'total_copies': total_copies,
            'available_copies': total_copies
        }

        return True, f"Success: Book '{title}' added successfully!"

    def add_member(self, name, email):
        """Add a member if ID is unique"""
        if not name or not email:
            return False, "Error: Name and email are required!"

        # Check if email already exists
        for member in self.members:
            if member['email'] == email:
                return False, "Error: Member with this email already exists!"

        # Generate unique member ID
        member_id = f"M{self.next_member_id:03d}"
        self.next_member_id += 1

        # Add member to list
        self.members.append({
            'member_id': member_id,
            'name': name,
            'email': email,
            'borrowed_books': []  # List of ISBNs
        })

        return True, f"Success: Member '{name}' added with ID: {member_id}"

    def search_books(self, search_type, search_term):
        """Search books by title or author"""
        if not search_term:
            return False, "Error: Search term cannot be empty!"

        results = []
        search_term = search_term.lower()

        if search_type == "title":
            # Search by title
            for isbn, book in self.books.items():
                if search_term in book['title'].lower():
                    results.append((isbn, book))
        elif search_type == "author":
            # Search by author
            for isbn, book in self.books.items():
                if search_term in book['author'].lower():
                    results.append((isbn, book))
        else:
            return False, "Error: Invalid search type!"

        return True, results

    def update_book(self, isbn, field, new_value):
        """Update book details"""
        if isbn not in self.books:
            return False, f"Error: Book with ISBN '{isbn}' not found!"

        book = self.books[isbn]

        try:
            if field == "title":
                if not new_value:
                    return False, "Error: Title cannot be empty!"
                book['title'] = new_value
                return True, "Title updated successfully!"

            elif field == "author":
                if not new_value:
                    return False, "Error: Author cannot be empty!"
                book['author'] = new_value
                return True, "Author updated successfully!"

            elif field == "genre":
                if new_value not in self.valid_genres:
                    return False, f"Error: Invalid genre! Must be one of: {self.valid_genres}"
                book['genre'] = new_value
                return True, "Genre updated successfully!"

            elif field == "total_copies":
                try:
                    new_copies = int(new_value)
                    if new_copies < 0:
                        return False, "Error: Copies cannot be negative!"

                    # Adjust available copies accordingly
                    borrowed_count = book['total_copies'] - book['available_copies']
                    book['total_copies'] = new_copies
                    book['available_copies'] = max(0, new_copies - borrowed_count)
                    return True, "Total copies updated successfully!"
                except ValueError:
                    return False, "Error: Total copies must be a number!"

            else:
                return False, "Error: Invalid field!"

        except Exception as e:
            return False, f"Error updating book: {e}"

    def update_member(self, member_id, field, new_value):
        """Update member details"""
        member = self._find_member_by_id(member_id)
        if not member:
            return False, f"Error: Member with ID '{member_id}' not found!"

        if field == "name":
            if not new_value:
                return False, "Error: Name cannot be empty!"
            member['name'] = new_value
            return True, "Name updated successfully!"

        elif field == "email":
            if not new_value:
                return False, "Error: Email cannot be empty!"

            # Check if email already exists (excluding current member)
            for m in self.members:
                if m['email'] == new_value and m['member_id'] != member_id:
                    return False, "Error: Email already exists!"

            member['email'] = new_value
            return True, "Email updated successfully!"

        else:
            return False, "Error: Invalid field!"

    def delete_book(self, isbn):
        """Delete book only if no borrowed items"""
        if isbn not in self.books:
            return False, f"Error: Book with ISBN '{isbn}' not found!"

        book = self.books[isbn]

        # Check if any copies are borrowed
        if book['available_copies'] < book['total_copies']:
            return False, "Error: Cannot delete book - some copies are currently borrowed!"

        del self.books[isbn]
        return True, "Book deleted successfully!"

    def delete_member(self, member_id):
        """Delete member only if no borrowed items"""
        member = self._find_member_by_id(member_id)
        if not member:
            return False, f"Error: Member with ID '{member_id}' not found!"

        # Check if member has borrowed books
        if member['borrowed_books']:
            return False, f"Error: Cannot delete member - they have {len(member['borrowed_books'])} borrowed book(s)!"

        self.members.remove(member)
        return True, "Member deleted successfully!"

    def borrow_book(self, member_id, isbn):
        """Member can borrow up to 3 books if available"""
        member = self._find_member_by_id(member_id)
        if not member:
            return False, f"Error: Member with ID '{member_id}' not found!"

        # Check if member has reached borrowing limit
        if len(member['borrowed_books']) >= 3:
            return False, "Error: You have reached the maximum borrowing limit of 3 books!"

        if isbn not in self.books:
            return False, f"Error: Book with ISBN '{isbn}' not found!"

        book = self.books[isbn]

        # Check if book is available
        if book['available_copies'] <= 0:
            return False, "Error: This book is currently not available!"

        # Check if member already borrowed this book
        if isbn in member['borrowed_books']:
            return False, "Error: You have already borrowed this book!"

        # Process borrowing
        book['available_copies'] -= 1
        member['borrowed_books'].append(isbn)

        return True, f"Success: Book '{book['title']}' borrowed successfully!"

    def return_book(self, member_id, isbn):
        """Return borrowed book"""
        member = self._find_member_by_id(member_id)
        if not member:
            return False, f"Error: Member with ID '{member_id}' not found!"

        if isbn not in member['borrowed_books']:
            return False, "Error: You haven't borrowed this book!"

        if isbn not in self.books:
            return False, "Error: Book not found in system!"

        # Process return
        member['borrowed_books'].remove(isbn)
        self.books[isbn]['available_copies'] += 1

        return True, f"Success: Book '{self.books[isbn]['title']}' returned successfully!"

    def get_book_details(self, isbn):
        """Get book details by ISBN"""
        return self.books.get(isbn)

    def get_member_details(self, member_id):
        """Get member details by ID"""
        return self._find_member_by_id(member_id)

    def get_all_books(self):
        """Get all books"""
        return self.books

    def get_all_members(self):
        """Get all members"""
        return self.members

    def _find_member_by_id(self, member_id):
        """Helper function to find member by ID"""
        for member in self.members:
            if member['member_id'] == member_id:
                return member
        return None

    def display_menu(self):
        """Display the main menu"""
        print("\n" + "=" * 50)
        print("      LIBRARY MANAGEMENT SYSTEM")
        print("=" * 50)
        print("1. Add Book")
        print("2. Add Member")
        print("3. Search Books")
        print("4. Update Book")
        print("5. Update Member")
        print("6. Delete Book")
        print("7. Delete Member")
        print("8. Borrow Book")
        print("9. Return Book")
        print("10. Display All Books")
        print("11. Display All Members")
        print("12. Exit")
        print("=" * 50)

    def run_interactive(self):
        """Interactive mode for user input"""
        print("Welcome to the Library Management System!")

        while True:
            self.display_menu()
            choice = input("Enter your choice (1-12): ").strip()

            try:
                if choice == "1":
                    self._interactive_add_book()
                elif choice == "2":
                    self._interactive_add_member()
                elif choice == "3":
                    self._interactive_search_books()
                elif choice == "4":
                    self._interactive_update_book()
                elif choice == "5":
                    self._interactive_update_member()
                elif choice == "6":
                    self._interactive_delete_book()
                elif choice == "7":
                    self._interactive_delete_member()
                elif choice == "8":
                    self._interactive_borrow_book()
                elif choice == "9":
                    self._interactive_return_book()
                elif choice == "10":
                    self._display_all_books()
                elif choice == "11":
                    self._display_all_members()
                elif choice == "12":
                    print("Thank you for using the Library Management System!")
                    break
                else:
                    print("Error: Please enter a valid choice (1-12)!")

            except Exception as e:
                print(f"An error occurred: {e}")

    def _interactive_add_book(self):
        """Interactive book addition"""
        print("\n--- Add New Book ---")
        isbn = input("Enter ISBN: ").strip()
        title = input("Enter title: ").strip()
        author = input("Enter author: ").strip()
        genre = input(f"Enter genre {self.valid_genres}: ").strip()
        try:
            total_copies = int(input("Enter total copies: "))
        except ValueError:
            print("Error: Total copies must be a number!")
            return

        success, message = self.add_book(isbn, title, author, genre, total_copies)
        print(message)

    def _interactive_add_member(self):
        """Interactive member addition"""
        print("\n--- Add New Member ---")
        name = input("Enter member name: ").strip()
        email = input("Enter email: ").strip()

        success, message = self.add_member(name, email)
        print(message)

    def _interactive_search_books(self):
        """Interactive book search"""
        print("\n--- Search Books ---")
        search_type = input("Search by (title/author): ").strip()
        search_term = input("Enter search term: ").strip()

        success, result = self.search_books(search_type, search_term)
        if success:
            if not result:
                print("No books found matching your search.")
            else:
                print(f"\nFound {len(result)} book(s):")
                for isbn, book in result:
                    available = book['available_copies']
                    total = book['total_copies']
                    status = "Available" if available > 0 else "Out of Stock"
                    print(f"ISBN: {isbn}, Title: {book['title']}, Author: {book['author']}, "
                          f"Genre: {book['genre']}, Available: {available}/{total} - {status}")
        else:
            print(result)

    def _interactive_update_book(self):
        """Interactive book update"""
        print("\n--- Update Book ---")
        isbn = input("Enter ISBN of book to update: ").strip()
        field = input("Enter field to update (title/author/genre/total_copies): ").strip()
        new_value = input("Enter new value: ").strip()

        success, message = self.update_book(isbn, field, new_value)
        print(message)

    def _interactive_update_member(self):
        """Interactive member update"""
        print("\n--- Update Member ---")
        member_id = input("Enter member ID to update: ").strip()
        field = input("Enter field to update (name/email): ").strip()
        new_value = input("Enter new value: ").strip()

        success, message = self.update_member(member_id, field, new_value)
        print(message)

    def _interactive_delete_book(self):
        """Interactive book deletion"""
        print("\n--- Delete Book ---")
        isbn = input("Enter ISBN of book to delete: ").strip()

        success, message = self.delete_book(isbn)
        print(message)

    def _interactive_delete_member(self):
        """Interactive member deletion"""
        print("\n--- Delete Member ---")
        member_id = input("Enter member ID to delete: ").strip()

        success, message = self.delete_member(member_id)
        print(message)

    def _interactive_borrow_book(self):
        """Interactive book borrowing"""
        print("\n--- Borrow Book ---")
        member_id = input("Enter your member ID: ").strip()
        isbn = input("Enter ISBN of book to borrow: ").strip()

        success, message = self.borrow_book(member_id, isbn)
        print(message)

    def _interactive_return_book(self):
        """Interactive book return"""
        print("\n--- Return Book ---")
        member_id = input("Enter your member ID: ").strip()
        isbn = input("Enter ISBN of book to return: ").strip()

        success, message = self.return_book(member_id, isbn)
        print(message)

    def _display_all_books(self):
        """Display all books"""
        print("\n--- All Books ---")
        books = self.get_all_books()
        if not books:
            print("No books in the library.")
            return

        for isbn, book in books.items():
            available = book['available_copies']
            total = book['total_copies']
            status = "Available" if available > 0 else "Out of Stock"
            print(f"ISBN: {isbn}, Title: {book['title']}, Author: {book['author']}, "
                  f"Genre: {book['genre']}, Available: {available}/{total} - {status}")

    def _display_all_members(self):
        """Display all members"""
        print("\n--- All Members ---")
        members = self.get_all_members()
        if not members:
            print("No members registered.")
            return

        for member in members:
            borrowed_count = len(member['borrowed_books'])
            print(f"ID: {member['member_id']}, Name: {member['name']}, "
                  f"Email: {member['email']}, Borrowed Books: {borrowed_count}")


# For direct execution
if __name__ == "__main__":
    library = LibraryManagementSystem()
    library.run_interactive()