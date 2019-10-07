# Sorting

## Q's to ask:
- how much data?
  - design for largest dataset you'll encounter
- does the data fit in memory?
  - if not, you'll need to split it into smaller chunks, sort, then merge
- is the data already mostly sorted?
  - some algorithms do this better than others, but hav epoor performance on random datasets
- how much extra memory is required?
- is relative order preserved?
  - if A_1 comes before A_2 in a presorted array, a __stable sort__ would keep that order

-----

# Algorithms

## Selection sort

- starts with first element & scans through array until it finds the element with the smallest key, then swaps the two
- runtime
  - first swap requires `n-1` comparisons, second swap requires `n-2`, etc.
  - `(n-1) + (n-2) + ... + 1 = n(n-1)/2 = O(n^2)`
- pros
  - simple to understand
  - at most `n-1` swaps --> in case moving elements is $$
  - in-place
  - not necessarily stable

```java
public void selectionSortRecursive(int[] data) {
	selectionSortRecursive(data, 0);
}

private void selectionSortRecursive(int[] data, int start) {
	if (start < data.length - 1) {
		swap(data, start, findMinIndex(data, start));
		selectionSortRecursive(data, start + 1);
	}
}

private int findMinIndex(int[] data, int start) {
	int minPos = start;
	for (int i = start+1; i < data.length; i++) {
		if (data[i] < data[minPos]) {
			minPos = i;
		}
	}
	return minPos;
}

private void swap(int[] data, int i1, int i2) {
	if (i1 != i2) {
		int temp = data[i1];
		data[i1] = data[i2];
		data[i2] = temp;
	}
}
```

## Insertion sort

- builds sorted array/list one element at a time by comparing each new element to already-sorted elements & inserts the new element into the correct location
- runtime
  - best = `O(n)`
  - avg = worst = `O(n^2)`
- pros
  - stable
  - in-place
  - good for sorting small datasets

```java
public void insertionSort(int[] data) {
	for (int which = 1; which < data.length; which++) {
		int val = data[which];
		for (int i = 0; i < which; i++) {
			if (data[i] > val) {
				System.arraycopy(data, i, data, i+1, which-1);
				data[i] = val;
				break;
			}
		}
	}
}
```

## Quicksort

## Merge sort

-----

# Problems

## Multi-key sort
You have an array of objects representing employees:
```java
public class Employee {
	public String firstName;
	public String lastName;
	public String phoneExtension;
}
```
Using a standard library sorting routine, sort the array so that it's ordered alphabetically by last name, then first name in a company phone book.

```java
input java.util.Comparator;

public class EmployeeNameComparator implements Comparator<Employee> {
	public int compare(Employee e1, Employee e2) {
		int ans = e1.lastName.compareToIgnoreCase(e2.lastName);
		if (ans == 0) {
			ans = e1.firstName.compareToIgnoreCase(e2.lastName);
		}
		return ans;
	}
}

// within some other class
	public void sortEmployees(Employee[] employees) {
		Arrays.sort(employees, new EmployeeNameComparator());
	}
```

Alternately, if you're using a stable sorting alg (like the modified merge sort used by `Arrays.sort()`), you can achieve the same result by calling sort 2x, first by last name, then by first name. This is possible because employees with the same last name would've maintained their relative ordering from the first sort.
