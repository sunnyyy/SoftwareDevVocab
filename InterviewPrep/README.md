# README.md

## Process

### 1. Examplify

### 2. Pattern match (to other algorithms)
- ex: modified binary search: for sorted array that's been rotated [3,4,5,6,7,1,2], find the minimum element
  - compare left vs. middle --> 3 & 6 --> 2nd half
  - compare (middle+1) vs. right --> 7 & 2 --> 2nd half

### 3. Simplify, then generalize
- ex: check if a ransom note (string of words) can be formed from a magazine (string of words)
  - instead of words, how would you check chars? --> array of 26 ints for the char counts
  - here, use HashMap of strings in the magazine

### 4. Base case, then build up
- ex: print out all permutations of a string
  - use recursion to generate all permutations of `s[1 .. n-1]`, then insert 
`s[n]` into every location of those strings

### 5. Data structure brainstorm
- ex: numbers are randomly generated & stored into an expanding array --> how do you keep track of the median?
- not LL
- not array, because you'd have to sort, which is $$
- binary tree? possibly, but it'd have to be perfectly balanced with median at top + caveat that median of a set of even number elements is `(m1 + m2) / 2`
- \*heap\* --> min heap for big half + max heap for small half --> median is easy to track --> heaps are easy to balance via `pop()`

## Misc tips

### StringBuffer / StringBuilder
- in Java, every time you append a string to a sentence, you create a copy of the sentence & run through all the letters to copy them over
- solution: use StringBuffer
- ex:
```java
public String makeSentence(String[] words) {
	StringBuffer sentence = new StringBuffer();
	for (String w : words) sentence.append(" "+w);
	return sentence.toString();
}