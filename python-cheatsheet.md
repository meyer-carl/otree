---
layout: default
title: "Python Cheat Sheet"
permalink: /python-cheatsheet/
---

# oTree Python Cheat-Sheet

A short explanation of the core Python concepts and code used in simple oTree apps, with examples.

---

## 0. Structure

- `settings.py`: governs the entire session, which can consist of multiple apps (games, surveys, etc.).
- **App**: Apps contain a part of your final experiment, such as a specific game.
    - How they work is determined in the `__init__.py` files (you can think of this as the backend).
    - How it looks to the participant is determined by the html files.

```bash
# Directory structure
my_project/
├── settings.py       # your oTree settings
├── survey/           # one app
│   └── __init__.py
│   └── demographics.html
└── dictator/         # another app
    └── __init__.py
```

In the apps we have looked at so far there are three important parts to each `__init__.py` file.
1. **Data**
- **Constants (`C`)**: for all your experimental constants.  
- **Models**:  
   - `Subsession` for round-level data (e.g., average contribution across all groups).  
   - `Group` for group-shared data (e.g., total contribution of the group).  
   - `Player` for individual player data (e.g., individual contribution of each player). 
2. **Utility functions** (e.g. `set_payoffs`) handle your payoff logic.  
3. **Pages**: define screens, use  
   - `form_model` + `form_fields` for input.  
   - `WaitPage` have everyone wait till others have caught up.  
   - `vars_for_template` to pass values into your HTML.  

---

## 1. oTree Module

In Python, a module is a file containing Python code that can be reused in your projects. oTree provides a module with all the essential tools for building experiments.

### Installing oTree
To use oTree, first install the package using pip:
```bash
pip install otree
```

### Importing oTree
Once installed, you can import all the core functions, classes, and constants from oTree with the following command:
```python
from otree.api import *
```

This import gives you access to key components such as `WaitPage`, `BasePlayer`, and other foundational elements, allowing you to focus on designing your experiment without reinventing the wheel.

In the following sections, we will explore the essential Python concepts you will frequently encounter when designing oTree experiments.

---

## 2. Variables & Constants

- **Variable**: freely named, changes over time.

  ```python
  total = 0
  name = "Alice"
  ```

- **Constant**: by convention, ALL_CAPS.

  ```python
  ENDOWMENT = cu(100)
  MULTIPLIER = 1.8
  ```

---

## 3. Data Types

| Type      | Example                           |
|-----------|-----------------------------------|
| Integer   | `42`                              |
| Float     | `3.14`                            |
| Currency  | `cu(50)`                          |
| String    | `"hello"`                         |
| List      | `[p.payoff for p in players]`     |
| Dict      | `{'age': player.age}`             |

### Lists
A list is an ordered collection of items that can hold elements of any data type. Lists are mutable, meaning you can modify their contents after creation. They are defined using square brackets `[]`.

Example:
```python
payoffs = [10, 20, 5]       # list of ints
players = ["Alice", "Bob"]  # list of strings
```

### Dictionaries (Dicts)
A dictionary is an unordered collection of key-value pairs. Keys must be unique and immutable (e.g., strings, numbers), while values can be of any data type. Dictionaries are defined using curly braces `{}`.

Example:
```python
player = {'name': 'Alice', 'age': 25}  # dict with string keys (name, age)
scores = {1: 100, 2: 200}             # dict with integer keys (1, 2)
```

#### Retrieving Data from a Dictionary

To retrieve data from a dictionary, you can use the key directly or use the `get` method to provide a default value if the key is missing.

Example:
```python
# Direct access (raises KeyError if key is missing)
age = player['age']

# Using get (returns None or default value if key is missing)
age = player.get('age', 'Unknown')  # Default to 'Unknown' if 'age' is not found
```

---

## 4. Functions

- Defined with `def name(arguments):`.  
- Use `return` to send back a value.

```python
@staticmethod
def is_displayed(player: Player):
    # This method shows the specific page only if it returns true.
    # The method takes a Player object as an argument. 
    # The Player object for each participant includes the role they have been assigned.
    # The page will only be displayed if the player's role matches the constant RECEIVER_ROLE.
    return player.role == C.RECEIVER_ROLE
    # We assigned C.RECEIVER_ROLE = 'Receiver'
    # For a receiver this player.role = 'Receiver'
    # So player.role == C.RECEIVER_ROLE evaluates to 'Receiver' == 'Receiver' which is true.
```
A function can also execute some code without returning anything.
```python
def set_payoffs(group: Group):
    """
    Calculate payoffs based on the offer and acceptance.
    If the offer is accepted, the proposer keeps the remainder,
    and the receiver gets the offer.
    If the offer is rejected, both earn zero.
    """
    # Get the proposers and receivers in the group
    proposer = group.get_player_by_role(C.PROPOSER_ROLE)
    receiver = group.get_player_by_role(C.RECEIVER_ROLE)
    if group.accept:
        # If accepted, proposer keeps the remainder, receiver gets the offer
        proposer.payoff = C.ENDOWMENT - group.offer
        receiver.payoff = group.offer
    else:
        # If rejected, both earn zero
        proposer.payoff = cu(0)
        receiver.payoff = cu(0)
```

## 5. Control Flow

**If/elif/else**:
The `if-elif-else` structure allows you to execute different blocks of code based on conditions. It evaluates each condition in order, and as soon as one is `True`, it executes the corresponding block and skips the rest.

In the `set_payoffs` function, we saw an example of an `if-else` structure. Here's another example that categorizes players based on their contributions:

```python
if player.contribution == 0:
    # If the player's contribution is exactly 0, they are labeled as a "Free rider."
    description = "Free rider"
elif player.contribution < C.ENDOWMENT:
    # If the player's contribution is greater than 0 but less than the total endowment, they are labeled as a "Contributor."
    description = "Contributor"
else:
    # If neither of the above conditions is true (i.e., the player contributed their full endowment), they are labeled as a "Full cooperator."
    description = "Full cooperator"
```

**For loops**:
A **for loop** is a way to repeat a block of code for each item in a collection (like a list, range of numbers, or group of objects). Think of it as saying, "Do this action for every item in the group."
```python
total = 0  # Initialize a variable 'total' to store the sum of contributions.
for p in group.get_players():  # Loop through each player in the group.
    total = total + p.contribution  # Add the 'contribution' attribute of each player to 'total'. Equivalent to total += p.contribution
```
Assume Alice contributed 20, Bob 50, and Charlie 10 and that the three of them form one group. The for loop iterates over the `players` list and accesses the `contribution` attribute of each `p` (player). The result is a sum of all the contributions.
1. iteration of the loop: p is Alice and p.contribution is Alice.contribution which is 20, so `total = 0 + 20 = 20`.
2. iteration of the loop: p is Bob and p.contribution is Bob.contribution which is 50, so `total = 20 + 50 = 70`.
3. iteration of the loop: p is Charlie and p.contribution is Charlie.contribution which is 10, so `total = 70 + 10 = 80`.

---

## 6. Lists & Comprehensions

List comprehensions provide a concise way to create lists using a loop. The syntax is `[expression for item in iterable]`.

For example, in otree each player has a `payoff` attribute. Assume Alice has a payoff of 100, Bob of 200, and Charlie of 150 and players contains these three players with their payoffs.

```python
# Create a list with all players in the group
players = group.get_players()
# Extracting payoffs
payoffs = [p.payoff for p in players] # Loops over the three players and generates a list with the results.

# Equivalent long form:
payoffs = []
for p in players:
    payoffs.append(p.payoff)
```
This creates a new list by iterating over the `players` list and accessing the `payoff` attribute of each `p` (assuming `players` is a list of objects, and each object has a `payoff` attribute). The result is a list of all `payoff` values from the `players` objects.
1. iteration of the loop: p is Alice and p.payoff is Alice.payoff which is 100
2. iteration of the loop: p is Bob and p.payoff is Bob.payoff which is 200
3. iteration of the loop: p is Charlie and p.payoff is Charlie.payoff which is 150
```python
print(payoffs)  # Output: [100, 200, 150]
```

---

## 7. Indentation & Blocks
In Python, indentation is used to define blocks of code. A block is a group of statements that belong together logically, such as the body of a function, loop, or conditional statement. Each block must be indented consistently.

#### Rules for Indentation:
1. Use spaces or tabs for indentation, but do not mix them in the same file (PEP 8 recommends 4 spaces per indentation level).
2. All lines in a block must have the same level of indentation.
3. Indentation errors will raise an `IndentationError`.

#### Example:
```python
# Correct indentation
if True:
    print("This is inside the block")  # Indented by 4 spaces
    print("This is also inside the block")

print("This is not inside the block")

# Incorrect indentation
if True:
    print("This is inside the block")
      print("This will raise an IndentationError")  # Misaligned
```

---

## 8. Comments & Docstrings

- **Single-line comment**: starts with `#`.  
- **Docstring**: triple-quoted string below a `def` or `class`. Typically explains what the code is doing.

```python
# This is a comment

def set_payoffs(group):
    """
    Calculates and assigns payoffs:
    - player 1 gets
    - player 2 gets
    """
    ...
```