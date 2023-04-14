# Approach
*Written by Saga Chandra from CSESoc Education*

Before we even consider writing up a solution to this problem, we first need to consider all the cases where Shaun cannot reach the end.

The first case that should immediately be obvious is when both sides of the road are blocked with sheep.
```
0 X 0
0 X 0
Where X is a sheep, and 0 is a free space of road.
```
However, because it is specified that Shaun's car cannot move diagonally, we must also consider the cases where
```
X 0 0
0 X 0
```
and
```
0 X 0
X 0 0
```
Hence, from these observations, we can conclude that for any sheep on the road, if any of the 3 opposing sides are also sheep, then Shaun cannot reach the end.
```
0 X 0
~ ~ ~
If any ~ is an X, then Shaun can never reach the end as the road is blocked.
```
Likewise, we need to do the same whenever a sheep gets blasted away from the road.
If we consider the observations above, we might derive that if a sheep is removed from the road, we cannot simply declare the road free again.
This is because every sheep has the potential to cause 3 separate blockages.
```
0 X 0
X X X
```
Hence, if Blamo removes a sheep from the second row, we would still have blockages on the road, and Shaun still cannot reach the end. Therefore, we need some way to keep track of the total amount of possible blockages on the road, and adjust this count according to whatever Blamo does.

Now that we have all the pieces of the puzzle, we can start piecing together a solution.

Firstly, we can `malloc()` our variable length array by initializing an array of `int*`.
```c
int *road[2] = { NULL }; // this notation initializes our array to NULL.
road[0] = malloc(cols * sizeof(int));
road[1] = malloc(cols * sizeof(int));
for (int i = 0; i < cols; ++i) {
    road[0][i] = 0;
    road[1][i] = 0;
    // initialises our array to 0
}
```
Alternative: You can just allocate it without risk of a stack overflow, `int road[2][10000]`.

For my solution, we will be using a psuedo-bitmap to store our information, where 0 represents an unblocked road, and 1 represents a sheep. 

Therefore, when we take in an action made by Blamo, we can simply reverse the value of our road, where
```c
// 0 (Not blocked) -> 1 (Is Blocked)
// 1 (Was Blocked) -> 0 (No Longer Blocked)
road[row-1][col-1] = !road[row-1][col-1];
```
Now onto the hard part, keeping track of all our blockages. From our planning beforehand, we know that whenever Blamo takes an action, we need to consider what happens with the 3 opposing sides. So lets write a function for that.
```c
int check_blocked(int *lane, int col) {
    int blocked = 0;
    for (int i = -1; i <= 1; ++i) {
        if (road[col+i] == 1) {
            blocked += 1;
        }
    }
    return blocked;
}
```
This code takes in a lane (one side of the road), and returns the number of blocked roads surrounding it and itself.
```
~ ~ ~
  ^
 We take in this road, and check the surrounding two roads.
```
But what do we do with this code?
Well, consider when a sheep is dropped onto a road. This can add a potential 3 blockages depending on the 3 roads opposite to it.
```c
if (road[row-1][col-1]) {
	blocked += res;
	// res here is the result of our function above.
}
```
Of course, we use the function above to find the total number of blockages we will form and add it to our total count of blockages!

This same principle can be applied to the opposite. Here we can reuse our code, and do the same thing but instead,
```c
if (!road[row-1][col-1]) {
	blocked -= res;
}
```
we minus from our blocked! Since Blamo removes a sheep, instead we check if any blockages are cleared.

The rest is simple, if our blocked is 0, then it clearly follows that Shaun can reach the end, and if our blocked is greater than 0, than it clearly follows that Shaun cannot reach the end. If your blocked is less than 0, then it clearly follows that your code has some problems somewhere.
```c
if (blocked) {
            printf("No\n");
        } else {
            printf("Yes\n");
        }
```
And we're done! Don't forget to free up your memory, and you're good to go! 

Congrats on getting through this article and understanding how to solve/solving (why are you reading the solution?) this pretty tricky question. 

Note: There are many other ways to solve this problem, some even with hashmaps (insert COMP2521 plug here), so if your solution doesn't exactly match this one, that's fine!
