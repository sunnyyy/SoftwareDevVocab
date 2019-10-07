# Recursion

-----

# Problems

## Generate the `n`-th Fibonacci number.
```java
public fib(int n) {					// O(n!)
	if (n < 0) throw Exception;
	if (n == 0) return 0;			// f(0) = 0	
	if (n == 1) return 1;			// f(1) = 1
	return fib(n-1) + fib(n-2);		// f(n) = f(n-1) + f(n-2)
}

public fibIterative(int n) {		// O(n)
	if (n < 0) throw Exception;
	if (n == 0) return 0;
	int a = 1, b = 1;
	for (int i = 3; i <= n; i++) {
		int c = a+b;
		a = b;
		b = c;
	}
	return b;
}
```

## Implement the "paint fill" function from MS Paint.

### Recursive solution
```java
public boolean withinBorder(int[][] canvas, int x, int y) {
	return x > 0 && x < canvas[0].length && y > 0 && y < canvas.length;
}

public void paintFillRecursive(int[][] canvas, int x, int y, int newColor, int oldColor) {
	// check within borders
	if (!withinBorder(canvas, x, y)) {
		return;
	}
	if (canvas[y][x] == oldColor) {
		canvas[y][x] = newColor;
		paintFillRecursive(canvas, x-1, y, oldColor, newColor);
		paintFillRecursive(canvas, x+1, y, oldColor, newColor);
		paintFillRecursive(canvas, x, y-1, oldColor, newColor);
		paintFillRecursive(canvas, x, y+1, oldColor, newColor);
	}
}
```

### Iterative solution (no real benefit since recursive soln is also `O(n)`)
```java
public void paintFillIterative(int[][] canvas, int x, int y, int newColor) {
	if (!withinBorder(canvas, x, y)) throw Exception;
	int oldColor = canvas[y][x];
	Queue<int[]> q = new Queue<>(); // memory O(n)
	int currentCell = {x, y};
	q.add(currentCell);
	while (q.peek() != null) {
		int[] currentCell = q.remove();
		int cx = currentCell[0];
		int cy = currentCell[1];
		if (!withinBorder(canvas, cx, cy) || canvas[cy][cx] != oldColor) continue;
		int[] north = {cx, cy-1};
		int[] south = {cx, cy+1};
		int[] east = {cx+1, cy};
		int[] west = {cx-1, cy};
		q.add(north);
		q.add(south);
		q.add(east);
		q.add(west);
	}
}
```

## Compute all permutations of a string.

```java
public static String insertCharAt(String word, char c, int i) {
	String start = word.substring(0, i);
	String end = word.substring(i);
	return start + c + end;
}

/**
 * permute("bc") = {"bc", "cb"}
 * permute("abc") = insert 'a' into each location within "bc" & "cb"
 */
public static ArrayList<String> permute(String s) {
	ArrayList<String> p = new ArrayList<>();
	if (s == null) throw Exception;
	if (s.length == 0) {
		p.add("")			// base case
		return p;
	}
	char first = s.charAt(0);
	String remainder = s.substring(1);
	ArrayList<String> words = permute(remainder);
	for (String word : words) {
		for (int i = 0; i < word.length(); i++) {
			p.add(insertCharAt(word, first, i));
		}
	}
	return p;
}
```

## Print all valid combos of n pairs of parentheses.

- e.g. input 3, output "()()()", "()(())", "(())()", "((()))"
- can insert left parens so long as we still have left parens remaining
- can insert right parens so long as # right < # left

```java
public static void printParentheses(int count) {
	char[] str = new char[count*2];
	parens(count, count, str, 0);
}

public static void parens(int l, int r, char[] str, int i) {
	if (l < 0 || r < 0) return;		// invalid state
	if (l == 0 && r == 0) {
		System.out.println(str);	// found one
	} else {
		if (l > 0) {
			str[i] = '(';
			parens(l-1, r, str, i+1);
		} else if (r > l) {
			str[i] = ')';
			parens(l, r-1, str, r+1);
		}
	}
}
```

## Given an infinite number of quarters, dimes, nickels, and pennies, calculate the # of ways to represent `n` cents.
```java
public static int makeChange(int n) {
	return makeChangeUS(n, 25);
}

public static int makeChangeUS(int n, int largestCoin) {
	int nextLargestCoin = 0;
	switch (largestCoin) {
		case 25:
			nextLargestCoin = 10;
			break;
		case 10:
			nextLargestCoin = 5;
			break;
		case 5:
			nextLargestCoin = 1;
			break;
		case 1:
			return 1;
	}
	int numWays = 0;
	for (int i = 0; i*largestCoin <= n; i++) {
		numWays += makeChangeUS( n - i*largestCoin, nextLargestCoin );
	}
	return numWays;
}
```