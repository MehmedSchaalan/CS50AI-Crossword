# Crossword

Given a text that represents a crossword structure and a text file with a domain of possible words, this function creates a crossword puzzle. The crossword/data folder contains these files.

By framing a crossword as a constraint fulfilment problem, an AI can find a solution to a given crossword using a given set of words, if one exists.

To satisfy unary constraints, node consistency is enforced, and the AC-3 algorithm is used to impose arc consistency to satisfy all binary constraints. Backtracking search was used to finish the entire word assignment.

Here, we have two important Python files in this project:

crossword.py: This file defines two classes, Variable and Crossword.
generate.py: Here, we define a class CrosswordCreator that we’ll use to solve the crossword puzzle.

## Background
How might you go about generating a crossword puzzle? Given the structure of a crossword puzzle (i.e., which squares of the grid are meant to be filled in with a letter), and a list of words to use, the problem becomes one of choosing which words should go in each vertical or horizontal sequence of squares. We can model this sort of problem as a constraint satisfaction problem. Each sequence of squares is one variable, for which we need to decide on its value (which word in the domain of possible words will fill in that sequence). Consider the following crossword puzzle structure.

In this structure, we have four variables, representing the four words we need to fill into this crossword puzzle (each indicated by a number in the above image). Each variable is defined by four values: the row it begins on (its i value), the column it begins on (its j value), the direction of the word (either down or across), and the length of the word. Variable 1, for example, would be a variable represented by a row of 1 (assuming 0 indexed counting from the top), a column of 1 (also assuming 0 indexed counting from the left), a direction of across, and a length of 4.

As with many constraint satisfaction problems, these variables have both unary and binary constraints. The unary constraint on a variable is given by its length. For Variable 1, for instance, the value BYTE would satisfy the unary constraint, but the value BIT would not (it has the wrong number of letters). Any values that don’t satisfy a variable’s unary constraints can therefore be removed from the variable’s domain immediately.

The binary constraints on a variable are given by its overlap with neighboring variables. Variable 1 has a single neighbor: Variable 2. Variable 2 has two neighbors: Variable 1 and Variable 3. For each pair of neighboring variables, those variables share an overlap: a single square that is common to them both. We can represent that overlap as the character index in each variable’s word that must be the same character. For example, the overlap between Variable 1 and Variable 2 might be represented as the pair (1, 0), meaning that Variable 1’s character at index 1 necessarily must be the same as Variable 2’s character at index 0 (assuming 0-indexing, again). The overlap between Variable 2 and Variable 3 would therefore be represented as the pair (3, 1): character 3 of Variable 2’s value must be the same as character 1 of Variable 3’s value.

For this problem, we’ll add the additional constraint that all words must be different: the same word should not be repeated multiple times in the puzzle.

The challenge ahead, then, is write a program to find a satisfying assignment: a different word (from a given vocabulary list) for each variable such that all of the unary and binary constraints are met.

### Logic
A crossword puzzle can be treated as a constraint satisfaction problem where each row or column where a word needs to fill up is seen as a variable/node with unary and binary constraints. The unary constraint is the length of each word and we just remove all the words in the domain of each variable/node to satisfy this constraint. The Binary constraint is that the words chosen for a pair(arc) of variables/nodes must have the same letter in the position that they intersect. If a word in the domain of node x has no possible words in the domain of node y in which the arc is consistent, that word must be removed from the domain of node x.

Once all arcs are consistent, we may use a search algorithm until a solution is found(or not found). Im my program I used backtracking search, where I iteratively chose a word to assign to a variable. If this assignment was possible/consistent, I would recursively call backtracking search. If the assignment is not consistent, I would remove the assignment. The function would return a list of assignments if a complete solution was found.

I also chose the variables/nodes in order of which had the smallest number of words left in their domain. I chose the first word assignment in order of which words would limit the least amount of words in the domains of neighboring variables. These would allow my algorithm to be slightly more efficient.

#### How does it work!

The Crossword class requires two values to create a new crossword puzzle: a structure_file that defines the structure of the puzzle (the _ is used to represent blank cells, any other character represents cells that won’t be filled in) and a words_file that defines a list of words (one on each line) to use for the vocabulary of the puzzle. Three examples of each of these files can be found in the data directory of the project. 
