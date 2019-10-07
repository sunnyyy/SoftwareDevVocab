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
