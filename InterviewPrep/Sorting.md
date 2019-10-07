# Sorting

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
