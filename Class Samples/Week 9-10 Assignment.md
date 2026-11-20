Week 9 - Assignment 5: File I/O and Unit Tests
Books are saved to a file and loaded back the next time the app runs, and our math and formatting are checked by automated tests

The menu and layout don't change this week. What changes is that the books are still there after you quit and start the program again, and you'll have tests that prove parts of your code work without typing anything in by hand.

First run (books.csv only has its header row):

% python book_tracker.py
========================================
  📚  YOUR LIBRARY
========================================

  What would you like to do?

  1) View books
  2) Add a book

  q) Quit

> 1
Your library is empty. Add a book first!

  What would you like to do?

  1) View books
  2) Add a book

  q) Quit

> 2
Book title: the long way to a small, angry planet
Author: Becky Chambers
Page count: 438

Book added:

  'The Long Way To A Small, Angry Planet' by Becky Chambers -- approx. 10.9 hours to read

  What would you like to do?

  1) View books
  2) Add a book

  q) Quit

> q
Goodbye!

Second run (the program was closed and started again):

% python book_tracker.py
========================================
  📚  YOUR LIBRARY
========================================

  What would you like to do?

  1) View books
  2) Add a book

  q) Quit

> 1
1. 'The Long Way To A Small, Angry Planet' - Becky Chambers (438 pages - approx. 10.9 hours to read)

  What would you like to do?

  1) View books
  2) Add a book

  q) Quit

> q
Goodbye!

What books.csv looks like after the first run:

title,author,pages,hours
"The Long Way To A Small, Angry Planet",Becky Chambers,438,10.9

Running the unit tests:

% pytest test_book_tracker.py
============================= test session starts ==============================
collected 6 items

test_book_tracker.py ......                                              [100%]

============================== 6 passed in 0.02s ===============================

Overview (from the client)
The client has been adding books through the menu and likes the flow. The problem is that every time they quit, their whole library disappears and they have to type everything in again. They want the app to remember their books between sessions.

"The menu is exactly what I wanted! The only thing is, when I quit, all my books are gone. Can you make it save what I add and have everything there the next time I open it?"

Our team lead has one more request. As the app grows, testing everything by hand every week takes longer and longer, and it's easy to break something that used to work without noticing.

"Before we add more features, let's get some automated tests in place. Start with the reading-time math and how a book is displayed. Those show up everywhere, so if they break, we want to know right away."

Plan — Business Requirements
Every book added through menu option 2 is saved to a file named books.csv right away, not just when the user quits
When the app starts, any books already in books.csv are loaded into the library list before the menu is shown
Viewing books after a restart shows every book added in earlier sessions, in the order they were added
Adding a book adds to the file and never erases books that were already saved
Book titles with commas in them (like The Long Way to a Small, Angry Planet) save and load correctly
books.csv has a header row (title,author,pages,hours) so the columns match the dictionary keys from Assignment 4
Loaded books have the same shape as newly added books: pages is a whole number and hours is a decimal number
A new format_book() function returns the numbered line of text for one book, and view_books() uses it
A test file named test_book_tracker.py checks estimate_reading_time() and format_book() with pytest, and every test passes
The menu, view, and quit behavior from Assignment 4 stay the same
Same workflow as before: new branch, PR, and main stays clean until review
Code
Use only what's found in these notes: https://cs50.harvard.edu/python/notes/5/ and https://cs50.harvard.edu/python/notes/6/

From the File I/O notes (6), everything you need is in the open, with, and CSV sections. You can skip the Binary Files and PIL section for this assignment.

From the Unit Tests notes (5), you'll use assert, pytest, and Testing Strings. You don't need Organizing Tests into Folders this week.

Before we code, let's make sure we're on the right branch. This week builds on your Assignment 4 code, so the new branch needs to start from wherever that code lives:

If your assignment_4 PR has been merged, your code is on main. Start from main.
If your assignment_4 PR is still open, your code is only on assignment_4. Start from assignment_4. If you start from main, your menu loop and view_books() from Assignment 4 won't be there.
VSCode:
If you're using VSCode:

Check the Source Control tab on the left
If there are changes, see if you should discard them or add them to the current branch
Click on the branch name in the bottom left and switch to main (if Assignment 4 was merged) or assignment_4 (if it wasn't)
Click the ... menu in the Source Control tab and choose Pull to get the latest changes from GitHub
Click on the branch name in the bottom left again and select create a new branch
Type assignment_5 and press enter
Open assignment/book_tracker.py and make sure your Assignment 4 functions are there
Terminal:
If you're using the terminal:

Check if there are any current changes and note our current branch:
git status

Switch to the branch that has your Assignment 4 code. Use main if Assignment 4 was merged, or assignment_4 if it wasn't:
git checkout main or git checkout assignment_4

Get any changes from the repo on GitHub:
git pull

Now create the new branch and switch to it:
git checkout -b assignment_5

Check that everything looks good:
git status

We should have no file changes and be on branch assignment_5. Open assignment/book_tracker.py and make sure your Assignment 4 functions are there.
Why CSV, and why the csv library?
In the notes, a plain text file with one name per line works fine until each line needs to hold more than one value. A CSV ("comma separated values") file holds one record per line, with each value separated by a comma.

Our books have a problem the notes run into too: the notes use the home "Number Four, Privet Drive", and we have the title The Long Way To A Small, Angry Planet. Both have a comma inside the value. If we wrote the file ourselves and read it back with .split(","), Python would think that title was two separate values. The notes show the fix: Python's built-in csv library. csv.DictWriter puts quotes around values that contain commas when it writes, and csv.DictReader understands those quotes when it reads. You don't need to add the quotes yourself.

A bonus: our books are already dictionaries from Assignment 4, and DictWriter and DictReader work with dictionaries.

Why unit tests?
So far, every test we've done has been a manual test: run the program, type things in, and check the output with our eyes. That works, but it's slow, and it only checks what we remember to check.

A unit test is code that checks other code. It calls one function (one "unit") with an input we choose and uses assert to check that the function gives back the answer we expect. pytest runs all of those checks at once and tells us which ones passed and which ones failed.

The notes point out an important rule: a function is much easier to test when it returns a value instead of printing it. A test can compare a returned value with ==, but it can't easily check what was printed to the screen. That's why this week we pull the line-building out of view_books() into a new format_book() function that returns the line as a string.

Let's code!
Step 1: Create books.csv
[ ] Create a new file named books.csv inside the assignment folder, next to book_tracker.py
[ ] Type this header row as the first and only line, then press Enter so the file ends with a new line:
title,author,pages,hours

The header row tells DictReader what to name each column. These names must match your dictionary keys from Assignment 4 exactly.

⚠️ Don't skip pressing Enter at the end of the csv header row. If the file doesn't end with a new line, the first book you save gets stuck onto the end of the header line and your file won't load correctly.

⚠️ Run the program and the tests from inside the assignment folder (cd assignment, then python book_tracker.py or pytest test_book_tracker.py), the same way as in Week 3 and 6. The program looks for books.csv in the folder you run it from, and the test file imports from book_tracker.py in the same folder. If you run from somewhere else, you'll see a FileNotFoundError or ModuleNotFoundError.

Step 2: Import the csv library
[ ] At the very top of book_tracker.py, add:
import csv

Step 3: Save each new book in add_book()
[ ] Update add_book(library) so that after it appends book to library, it also writes book to books.csv
[ ] Open books.csv in append mode ("a") using with. Append mode adds to the end of the file. Write mode ("w") would erase every book already saved, so don't use it here.
[ ] Inside the with block, create a csv.DictWriter that takes the file and fieldnames=["title", "author", "pages", "hours"]
[ ] Call writerow() on the writer and pass in the book dictionary
[ ] The Book added: message and summary line stay the same as Assignment 4
def add_book(library):
    """
    Get the book details from the user, add the book to library,
    save it to books.csv, and print a summary.
    """
    # --- From Assignment 4 (keep what you already have) ---
    # get title, author, and pages from the user
    # call estimate_reading_time to get hours
    # create the dictionary book with keys "title", "author", "pages", "hours"
    # append book to library

    # --- New this week ---
    # open "books.csv" in append mode using with ... as file:
        # create a variable called writer that holds a csv.DictWriter
        #     pass in the file and fieldnames=["title", "author", "pages", "hours"]
        # use writer.writerow() to write the book dictionary to the file

    # --- From Assignment 4 ---
    # print "Book added:" followed by the summary line

Step 4: Create a load_books() function
[ ] Create a load_books() function that reads books.csv and returns a list of book dictionaries
[ ] Start with an empty list named library
[ ] Open books.csv using with. When you are only reading, you can leave out the mode or use "r".
[ ] Create a csv.DictReader that takes the file. Each time through a for loop, it gives you one row as a dictionary, using the header row for the keys. It skips the header row for you.
[ ] For each row, build a book dictionary with the same four keys as add_book() and append it to library
[ ] Return library
Everything read from a file comes back as a string, even numbers. row["pages"] is "438", not 438. Use int() and float() from Week 3 to convert them back, so a loaded book looks exactly like a book you just added:

def load_books():
    """
    Read every book saved in books.csv and return them as a list of dictionaries.
    """
    # create an empty list called library

    # open "books.csv" using with ... as file:
        # create a variable called reader that holds csv.DictReader(file)
        # for each row in reader:
            # create a dictionary called book:
            #     "title"  -> row["title"]
            #     "author" -> row["author"]
            #     "pages"  -> row["pages"] converted with int()
            #     "hours"  -> row["hours"] converted with float()
            # append book to library

    # return library

Step 5: Create a format_book() function and use it in view_books()
Right now view_books() builds each line with an f-string and prints it right away. We're going to move the f-string into its own function that returns the line instead, so we can test it.

[ ] Create a format_book() function that takes in a book dictionary and a number
[ ] It returns (does not print) the line in the same format as Assignment 4:
1. 'The Long Way To A Small, Angry Planet' - Becky Chambers (438 pages - approx. 10.9 hours to read)

[ ] Update view_books() so that inside its loop, it prints whatever format_book() returns. Remember to pass in i + 1 so the numbers start at 1.
def format_book(book, number):
    """
    Return one book as a numbered line of text.
    """
    # return an f-string using number, book["title"], book["author"],
    # book["pages"], and book["hours"] in the format shown above


def view_books(library):
    """Print every book in library with a number in front, or a message if it's empty."""
    # --- From Assignment 4 ---
    # if the library is empty, print the empty message
    # otherwise, loop over range(len(library)):

        # --- Changed this week ---
        # print the value returned from format_book(library[i], i + 1)

The program's output should look exactly the same as before. Run it and press 1 to check. Changing how code is organized without changing what it does is called refactoring, and it's one of the best reasons to have tests.

in main()
[ ] Instead of starting library as an empty list, fill it from the file with load_books()
[ ] Nothing else in main() changes. view_books(library) and add_book(library) already use library.
in our main function:
...
    instead of:  create an empty list with the name library
    now:         set library equal to the list returned from load_books()

    call the function to show the dashboard

    the menu loop stays exactly the same as Assignment 4

Step 6: pytest
[ ] Make sure your venv from Week 1 is active (you should see (.venv) or similar at the start of your terminal line)
Step 7: Write test_book_tracker.py
[ ] Create a new file named test_book_tracker.py inside the assignment folder, next to book_tracker.py. pytest looks for files and functions whose names start with test_.
[ ] At the top, import pytest and the two functions you're testing from book_tracker:
import pytest

from book_tracker import estimate_reading_time, format_book

Importing from book_tracker does not start the menu. That's what the if __name__ == "__main__": line at the bottom of book_tracker.py has been doing since Week 3: main() only runs when you run the file directly, not when another file imports it.

[ ] Write at least six test functions. Like the notes, split your checks into separate functions so pytest can tell you exactly which kind of input failed. Every assert compares what a function returns with the answer you expect.
# --- estimate_reading_time ---

def test_estimate_whole_hours():
    # assert that 40 pages returns 1.0
    # assert that 400 pages returns 10.0


def test_estimate_rounds_to_one_decimal():
    # assert that 438 pages returns 10.9
    # assert that 412 pages returns 10.3


def test_estimate_zero():
    # assert that 0 pages returns 0.0


def test_estimate_str():
    # use with pytest.raises(TypeError):
        # call estimate_reading_time with a string like "cat"
    # (you can't divide a string by 40, so Python should raise a TypeError)


# --- format_book ---

def test_format_book():
    # create a book dictionary for Dune by Frank Herbert, 412 pages, 10.3 hours
    # assert that format_book(book, 1) returns exactly:
    #   "1. 'Dune' - Frank Herbert (412 pages - approx. 10.3 hours to read)"


def test_format_book_comma_in_title():
    # create a book dictionary for The Long Way To A Small, Angry Planet
    #   by Becky Chambers, 438 pages, 10.9 hours
    # assert that format_book(book, 2) returns the line starting with "2. "

💡 Check that your tests can fail. A test that always passes isn't testing anything. Try temporarily changing 40 to 50 in estimate_reading_time() and run pytest again. You should see your estimate tests fail. Change it back before you commit.

[ ] From inside the assignment folder, run:
pytest test_book_tracker.py

[ ] Every test should pass. If one fails, pytest shows you the assert that failed and the value your function actually returned. Fix the function (or the test, if the test was wrong) and run it again.
Build — Pushing to GitHub
While Python is an interpreted language and doesn't have a build step, for this class we can think of the "Build" Phase of DevOps being us sharing the code to a repo.

Make sure books.csv and test_book_tracker.py are both included in your commit. I need them to run your program and your tests.

git status
git add .
git commit -m "save and load books with books.csv, add format_book and unit tests"
git push

Once we have pushed, we can now go over to GitHub and open a PR. This lets the team (me) know that the code is ready for review.

When you open the PR, check that it's comparing assignment_5 against main.

Test — Automated
[ ] pytest test_book_tracker.py runs at least six tests, and all of them pass
[ ] Temporarily breaking estimate_reading_time() makes the estimate tests fail (then change it back)
[ ] Temporarily changing the f-string in format_book() makes the format_book tests fail (then change it back)
Test — Manual
Start each test with books.csv holding only the header row title,author,pages,hours (followed by a new line).

[ ] Starting the program and pressing 1 says the library is empty and returns to the menu
[ ] Pressing 2 and adding a book prints a correct summary and returns to the menu
[ ] After adding a book, opening books.csv in VS Code shows a new line for that book under the header row
[ ] Adding a book with a comma in the title (try the long way to a small, angry planet) saves that title in quotes in books.csv
[ ] Quitting with q and starting the program again, then pressing 1, shows the book from the last session
[ ] Adding a second book in the new session and pressing 1 shows both books, numbered 1 and 2, in the order they were added
[ ] Quitting and starting a third time still shows both books, and books.csv has exactly two book lines (nothing was erased or duplicated)
[ ] Loaded books show page counts as whole numbers (438 pages, not 438.0 pages) and reading time as a decimal (10.9 hours)
[ ] Typing an option that isn't on the menu still prints a message and shows the menu again
[ ] Typing q, quit, or exit still quits the program