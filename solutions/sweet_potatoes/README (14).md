# Approach
*Written by Saga Chandra from CSESoc Education*

The aim of the problem is to find the maximum sum in the cross pattern provided. 

The first hurdle in this problem is how we take in and store the information we receive.

Firstly, note that we will need to dynamically allocate memory by using `malloc()` as we want to store our map in a variable-length 2D array (variable length arrays are a no-no in 1511 and in programming generally). 

Alternative: `int arr[10000][10000]` does not result in a stack overflow, so that also works :).

We can do this by firstly creating an array of integer pointers that act as our rows, and then dynamically allocating memory for our columns.
```c
int **town_hall = malloc(rows * sizeof(int*));
// Allocating our rows
for (int i = 0; i < rows; ++i) {
	town_hall[i] = malloc(cols * sizeof(int)); 
	// Allocating our columns
}
```
Next, note that we are allowed to place our stall in a position where it might not necessarily form an X pattern. Therefore, we will need a function to test whether or not our position is a valid point in our array.
```c
int valid_index(int row, int col, 
                int rows, int cols) {
	return ((0 <= row) && (row < rows) &&
	       (0 <= col) && (col < cols));
}
```
Note: You'll see that this might be very similar to your valid_point() in 1511 Assignment 1.

Now onto the bulk of the problem, getting the sum of our X shape from the array! You could find a fancy pattern and use a bunch of for loops to solve it, but I opted for a bunch of if statements. 
This task function takes in a position in our array, and evaluates the X pattern's sum.
```c
int eval_x(int row, int col, 
           int max_row, int max_col,
           int **town_hall) {
    int local_max = 0;
    if (valid_index(row-1, col-1, max_row, max_col)) {
        local_max += town_hall[row-1][col-1];
    }
    if (valid_index(row-1, col+1, max_row, max_col)) {
        local_max += town_hall[row-1][col+1];
    }
    if (valid_index(row+1, col-1, max_row, max_col)) {
        local_max += town_hall[row+1][col-1];
    }
    if (valid_index(row+1, col+1, max_row, max_col)) {
        local_max += town_hall[row+1][col+1];
    }
    return local_max + town_hall[row][col];
}
```
Remember that they must be `if` statements, and not `else if`, otherwise you won't be calculating the sum.

We're almost there, all that's left is to find the maximum value of all our sums. 
We can write a helper function to simplify the logic just a tad!
```c
	int max_i(int a, int b) {
    if (a > b) {
        return a;
    }
    return b;
}
```
And we're basically done! 

Here, we can iterate through every position in our array, and calculate the X pattern's sum for each position, checking if it is higher than the previously highest calculated sum. If it is, we store the row and column of that position (remember, we need these for our output), set our new maximum to that value, and continue iterating through our 2D array!
```c
int global_max = 0;
    int global_row = 0, global_col = 0;
    for (int row = 0; row < max_row; ++row) {
        for (int col = 0; col < max_col; ++col) {
            int local_max = eval_x(row, col, max_row, max_col, town_hall);
            if (max_i(global_max, local_max) == local_max) {
                global_max = local_max;
                global_row = row;
                global_col = col;
            }
        }
    }
```
Wait, wait wait. Not so fast, remember when we dynamically allocated our 2D array with `malloc()`? Well, we need to free that memory so that we don't cause any memory leaks!
We can write a simple helper function to help us do this,
```c
void free_all(int **town_hall, int max_row) {
    for (int i = 0; i < max_row; ++i) {
        free(town_hall[i]);
    }
    free(town_hall);
}
```
Remember that we dynamically allocated an `int*` array AND the `int`s stored inside our `int*`, so we will need to free both.

And now, you're finally done! If you made it this far, congrats on understanding how to solve this relatively tricky 2D array problem! You'll be a master of them in no time!
Good luck on your 1511 finals!


