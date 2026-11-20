## Week 6 - Assignment 4: Conditionals and Loops
UI for menu and expected layout

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

> 1
1. 'The Long Way To A Small, Angry Planet' - Becky Chambers (438 pages - approx. 10.9 hours to read)

  What would you like to do?

  1) View books
  2) Add a book

  q) Quit

> 5
Sorry, that option isn't available.

  What would you like to do?

  1) View books
  2) Add a book

  q) Quit

> q
Goodbye!


## Overview (from the client)
The client has been trying out the add_book flow and they're happy with how it looks. Now they want to do more with it. Right now the app runs once: it adds a single book and then ends. They'd like to add several books in one sitting, look back at everything they've added so far, and decide for themselves when they're done.

> "Adding a book works great! Next I'd like a menu so I can add a few books, pull up a list of what I've added, and quit when I'm ready. Keep it simple, I just want to see the flow."

## Plan — Business Requirements
- A menu shown after the dashboard with options to view books, add a book, or quit
- The app keeps running until the user chooses to quit, with no restarting to add another book
- Books are held in a library list while the app runs, with each book stored as a dictionary
- Users can view every book they've added, including author, page count, and estimated reading time
- Viewing an empty library tells the user to add a book first instead of showing nothing
- An option that isn't on the menu gets a friendly message, and the menu shows again
- q, quit, or exit all close the app with a goodbye message
- Books only need to last while the app is running; saving between sessions isn't required yet
- Same workflow as before: new branch, PR, and main stays clean until review
## Code
Use only what's found in the notes we've covered so far: https://cs50.harvard.edu/python/notes/0/, https://cs50.harvard.edu/python/notes/1/, and https://cs50.harvard.edu/python/notes/2/

Before we code, let's make sure we're on the right branch. This week builds on your Week 3 code, so the new branch needs to start from wherever that code lives:

- If your assignment_3 PR has been merged, your code is on main. Start from main.
- If your assignment_3 PR is still open, your code is only on assignment_3. Start from assignment_3. If you start from main, your book_tracker.py from Week 3 won't be there.
## VSCode:
If you're using VSCode:

- Check the Source Control tab on the left
- If there are changes, see if you should discard them or add them to the current branch
- Click on the branch name in the bottom left and switch to main (if Week 3 was merged) or assignment_3 (if it wasn't)
- Click the ... menu in the Source Control tab and choose Pull to get the latest changes from GitHub
- Click on the branch name in the bottom left again and select create a new branch
- Type assignment_4 and press enter
- Open assignment/book_tracker.py and make sure your Week 3 functions are there
## Terminal:
If you're using the terminal:

- Check if there are any current changes and note our current branch:
git status

- Switch to the branch that has your Week 3 code. Use main if Week 3 was merged, or assignment_3 if it wasn't:
git checkout main or git checkout assignment_3

- Get any changes from the repo on GitHub:
git pull

- Now create the new branch and switch to it:
git checkout -b assignment_4

- Check that everything looks good:
git status

- We should have no file changes and be on branch assignment_4. Open assignment/book_tracker.py and make sure your Week 3 functions are there.
## Let's code!
[ ] Create a show_menu() function that prints the following, asks the user for input with input("> "), and returns what they typed:
  What would you like to do?

  1) View books
  2) Add a book

  q) Quit


[ ] Use .strip() and .lower() on the input before returning it so Q and q work the same as q

[ ] Update the add_book() function to take in a list named library

[ ] Create a dictionary called book
[ ] Store the user's input and the reading time in book using these keys: "title", "author", "pages", and "hours". These match the variable names from Week 3.
[ ] Add the dictionary book to the list library using .append(). We should now have a list that holds dictionaries.
[ ] Print Book added: followed by the summary line. Check your Week 3 f-string: it should say approx. {hours} hours to read, with the word "hours" after the number, to match the expected output.
[ ] Create a view_books() function that takes in a list named library

[ ] If the list is empty, print Your library is empty. Add a book first!
[ ] Otherwise, loop over the list and print each book with a number in front, starting at 1. range(len(library)) from the Week 5 notes gives you a number for each book; remember to add 1 so the list doesn't start at 0.
[ ] Each book should print in this format, using the values from its dictionary:
1. 'The Long Way To A Small, Angry Planet' - Becky Chambers (438 pages - approx. 10.9 hours to read)

## in main
[ ] Create a list called library in main() to hold library books
[ ] Create a loop in main() that lets users choose from the options 1, 2, and q, and shows the menu again after each choice

in our main function:
...
    create an empty list with the name library 

    call the function to show the dashboard

    we'd like a continuous loop for the user:

        set a variable to hold the value returned from `show_menu()`

        conditional logic where the variable is equal to "1" 
            this should call the function view_books(library)
        
        conditional logic where the variable is equal to "2"
            this should call the function add_book(library)
        
        conditional logic where the variable is equal to "q", or "quit", or "exit"
            this should print("Goodbye!")
            this should stop the loop so the program ends
        
        conditional logic where the variable is some other option not defined above
            this should print("Sorry, that option isn't available.") and go back into the loop

## Build — Pushing to GitHub
While Python is an interpreted language and doesn't have a build step, for this class we can think of the "Build" Phase of DevOps being us sharing the code to a repo.

git status
git add .
git commit -m "menu loop, library list, show_menu and view_books functions"
git push

Once we have pushed, we can now go over to GitHub and open a PR. This lets the team (your instructor) know that the code is ready for review.

When you open the PR, check that it's comparing assignment_4 against main. If your assignment_3 PR is still open, this PR will show your Week 3 changes too. That's expected.

## Test — Manual
[ ] Pressing 1 without having added a book tells the user to add a book first and returns to the main loop
[ ] Pressing 2 allows you to add a book
[ ] Entering a title/author/page count prints a correct summary and continues to the main loop
[ ] Pressing 1 after adding a book shows the numbered list of books and continues to the main loop
[ ] Adding a second book and pressing 1 shows both books, numbered 1 and 2
[ ] Typing an option that isn't on the menu (like 5 or hello) prints a message and shows the menu again
[ ] Pressing q allows you to quit the program
[ ] Typing quit or exit also quits the program
## GRADING
A note on grading: I review PRs the day after they are submitted (M-Th). If you submit the assignment before Friday of the week this means I can review and grade it and if you are missing points you will have the weekend to update. If you make the changes I've asked for I will bump your grade up as long as your commit is turned in before Monday.

| **Points** | **Description** |
|---|---|
| **Moodle** | Turn in through Assignment 4 in Week 6 |
| 10 | Provide GitHub link to PR |
| 10 | Provide screenshot of Python code running in your terminal |
| **GitHub/Code** | Code will be reviewed on GitHub |
| 10 | GitHub PR branch is named `assignment_4` |
| 10 | GitHub PR is left open and not merged until I review |
| 10 | `main` function has a repeating loop for user options |
| 10 | Conditional logic in the loop takes user input and chooses the appropriate next step or function |
| 10 | Updated `add_book` function takes in a list called `library` and appends the dictionary book to it |
| 10 | `show_menu` function displays the options and the full text provided for `"1"`, `"2"`, and `"q"`, and returns the user’s choice |
| 10 | `view_books` function takes in a list and prints the books, or a message if the list is empty |
| 10 | Manual test results are pasted into a GitHub comment from your terminal |