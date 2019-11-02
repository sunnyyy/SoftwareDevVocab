# Math

-----

# Problems

## Convert String to int --> `Integer.parseInt()`
```java
public static int stringToInteger(String s) {
	if (s == null || s.isEmpty()) throw Exception;
	int n = 0;
	boolean neg = false;
	if (s.charAt(0) == '-') {
		neg = true;
	}
	int endChar = neg ? 0 : 1;
	for (int i = s.length()-1; i >= endChar; i--) {
		if (s.charAt(i) < '0' || s.charAt(i) > '9') throw Exception;
		n *= 10;
		n += s.charAt(i) - '0';
	}
	if (neg) num = -num;
	return num;
}
```

## Convert int to String --> `Integer.toString()`
```java
// import java.util.*;
public static String integerToString(int n) {
	if (n == null) throw Exception;
	boolean neg = n < 0;
	String s = "";
	n = Math.abs(n);
	while (n%10 > 0 || n/10 > 0) {
		int remainder = n%10;
		s = remainder + s;
		n /= 10;
	}
	return (neg ? "-"+s : s);
}
```

## Convert String equation to numerical value
Create a function that would take a string like "3 + 10 / 2 - 1 * 6" and return 2 (the mathematical value of the equation).

- Can I assume that all values will be separated by a space? Yes.
- Which operators should I account for? For now, just `+ - * /`.
- No parentheses? Not now.
- Should I assume that division should mean integer division, or double division? Assume doubles.
- Can I assume that all inputs are valid? Yes.
  - This means I can assume that, once I split the string into an array, that all the numbers will be in the even indices of the array (starting at 0) and that the operators will be in the odd indices of the array, correct? Yes.

```java
import java.util.*;

public class Solution {

	// We go through the array 2x, once for * and / and then once for + and -
	// due to PEMDAS rule. To do that, we save the value of the array once.
	public static int stringEquationToDouble(String s) {
		String[] a = s.split(" ");
		double[] d = new double[a.length];

		// set + - * / to specific values, it doesn't matter
		double add = 1.0;
		double sub = 2.0;
		double mult = 3.0;
		double div = 4.0;

		// actually first go through string array, convert everything to a double
		// (tbh this solution would've been better a list but too late, it's O(n^2) now)
		for (int i = 0; i < a.length; i++) {
			switch (a[i]) {
				case "+":
					d[i] = add;
					break;
				case "-":
					d[i] = sub;
					break;
				case "*":
					d[i] = mult;
					break;
				case "/":
					d[i] = div;
					break;
				default:
					d[i] = Double.parseDouble(a[i]);
					break;
			}
		}

		// first pass through double array to find multiplication, division
		// we'll need to do two sequential for loops due to PEMDAS
		// only look at odd indices
		for (int i = 1; i < a.length; i += 2) {

			// we found either multiplication or division
			if (d[i] == mult || d[i] == div) {

				// the previous number will be at some index to the left
				// and the next number will be at i+1

				// find previous index 
				int prevI = i-1;
				while (d[prevI] == null) {			// worst case decrement n iterations
					prevI--;						// making this an O(n^2) solution
				}
				
				// perform the multiplication or division
				// (yes, i know we just checked d[i] but this way looks cleaner)
				if (d[i] == mult) {
					d[prevI] = d[prevI] * d[i+1];
				} else {
					d[prevI] = d[prevI] / d[i+1];
				}

				// set d[i] and d[i+1] to null (where i is odd and i+1 is even)
				d[i] = null;
				d[i+1] = null;
			}
		}

		// second pass through double array to find addition, subtraction
		// only look at odd indices
		for (int i = 1; i < a.length; i += 2) {

			// we found either addition or subtraction
			// at this point we should do a null check since we could've made the
			// value at the odd index null
			if (d[i] != null && (d[i] == add || d[i] == sub)) {

				// the previous number will be at some index to the left
				// and the next number will be at i+1

				// find previous index 
				int prevI = i-1;
				while (d[prevI] == null) {			// worst case decrement n iterations
					prevI--;						// making this an O(n^2) solution
				}
				
				// perform the addition or subtraction
				// (yes, i know we just checked d[i] but this way looks cleaner)
				if (d[i] == add) {
					d[prevI] = d[prevI] + d[i+1];
				} else {
					d[prevI] = d[prevI] - d[i+1];
				}

				// set d[i] and d[i+1] to null (where i is odd and i+1 is even)
				d[i] = null;
				d[i+1] = null;
			}
		}

		// at this point, you'll have an array where the first element is a double
		// and all other elements are null, because you always saved the results
		// at the leftmost index of the array
		return d[0];
	}
}
```