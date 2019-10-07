# Object-Oriented Programming

## Inheritance (Java "extends")
- class B inherits from class A
  - class A is B's __*parent*__ or __*base*__
  - class B is A's __*subclass*__
- all behaviors from class A are present in class B, though possibly modified
- an instance of class B can be used where an instance of class A is required

## Polymorphism
- ability to provide multiple implementations of an action
- e.g. an abstract class Shape with implementations Rectangle, Circle, Triangle
- construction / deconstruction
- __interface__ = declares a set of methods, outside of any class
- __abstract class__ = incomplete class that declares (but does not define) all its methods

-----

# UML class diagrams

- rectangle with 3 compartments
  - top compartment = name of the class
  - middle compartment = attributes (aka class variables)
  - bottom compartment = operations (aka methods)
- symbols
  - \+ public
  - \- private
  - \# protected
  - \~ package

-----

# Problems

## Design the data structure for a generic deck of cards

```java
public class Card {
	public CardValue value;
	public CardSuit suit;
	public Comparator comparator;
}

public class Deck {

	public ArrayList<Card> cardsInDeck;

	public Deck() {
		// generates default deck with 52 cards
	}

	public Deck(ArrayList<Card> cards) {
		this.cardsInDeck = cards;
	}

	public void add(Card card) {
		cardsInDeck.add(card);
	}

	public boolean removeCard(Card card) {
		cardsInDeck.remove(card);			// runs in O(n)
	}

	public void shuffle() {
		Collections.shuffle(cardsInDeck);	// runs in O(n)
	}
}
```

### How would I subclass this to implement particular card games?

```java
// create new card object that inherits from base class of card
// e.g. for Blackjack, Hearts, etc.
public class CardWithNumericValue extends Card {
	public int numValue;
	public Comparator comparator;
}

// create new object for players
public class Player {
	public String userId;
	private ArrayList<Card> hand; 
}
```

## Implement a call center.

Imagine you have a call center with 3 levels of employees: newbie, team lead, and PM. If a newbie can't handle a call, escalate to team lead. If a team lead can't handle a call, escalate to PM. There's only one PM and one team lead. Only free workers can answer phones. Design the calsses & data structures. Implement `getCallHandler()`.

```java
public class Employee {				// tbh these vars should all be private
	public boolean isAvailable;		// & we should use getters/setters
	public int level;
	public String username;
}


public class CallCenter {
	private Employee lead;
	private Employee pm;
	private ArrayList<Employee> newbies;
	private HashMap<Employee, String> allOngoingCalls;
	
	public CallCenter(Employee lead, Employee pm, ArrayList newbies) {
		this.lead = lead;
		this.pm = pm;
		this.newbies = newbies;
	}

	public Employee getCallHandler(String callId) {
		if (callId == null || callId.isEmpty()) throw Exception;

		// ...
	} 

}
```

## Design a chess game.
```java
import java.awt.geom.Point2D;

public class ChessPiece {

	public int id;
	public Point location;
	public ChessPieceColor color;
	public ChessPieceValue value;

	public enum ChessPieceColor {
		BLACK, WHITE;
	}
	public enum ChessPieceValue {
		PAWN, ROOK, KNIGHT, BISHOP, QUEEN, KING;
	}
}

// ----------

import java.lang.Math;

public class Chess {

	int[][] board = new int[8][8];
	HashSet<Piece> piecesInPlay;
	// some other setup stuff

	private boolean isInSameRowOrCol(int px, int py, int x, int y) {
		return (px == x) || (py == y);
	}

	private boolean isInSameDiagonal(int px, int py, int x, int y) {
		// todo
	}

	private boolean couldDieInNextMove(int x, int y) {
		// todo
		// iterate through piecesInPlay to check
	}

	public boolean isCellOccupied(int x, int y) {
		return board[x][y] == 0;
	}

	public boolean validMove(ChessPiece piece, int x, int y) {
		int px = piece.getLocation().getX();
		int py = piece.getLocation().getY();
		int dx = x - px;
		int dy = y - py;
		if (dx == 0 && dy == 0) return false;
		switch (piece.getValue()) {
			case KING:
				return (Math.abs(dx) <= 1 && Math.abs(dy) <= 1)
						&& couldDieInNextMove(x,y);
			case QUEEN:
				return isInSameRowOrCol(px, py, x, y) || isInSameDiagonal(px, py, x, y);
			case ROOK:
				return isInSameRowOrCol(px, py, x, y);
			case BISHOP:
				return isInSameDiagonal(px, py, x, y);
			case KNIGHT:
				if ( (Math.abs(dx) == 2 && Math.abs(dy) == 3)
					 || (Math.abs(dx) == 3 && Math.abs(dy) == 2) ) {
					return true;
				}
				break;
			case PAWN:
				if ( (dx == 0 && dy == 1 && !isCellOccupied(x,y))
					 || (dx == 1 && dy == 1 && isCellOccupied(x,y)) ) {
					return true;
				}
				break;
		}
		return false;
	}
}
```

## Design the data stuctures for an online book reader system.
```
BOOK
+ String id
+ String title
+ String subtitle
+ List<String> authors
+ String publisher
+ DateTime publishDate
+ int numPages
+ List<String> chapterTitles
+ List<String> text
+ Price price
+ boolean owned(User user)

READER UI
+ Book currentBook
+ String pageText
+ int fontSize
+ Button nextButton
+ Button prevButton
+ int currentPage

LIBRARY UI
+ int booksPerPage
+ int librarySize
+ Set<Comparator>		--> for sorting through books

STORE UI
+ List<Books> topBooks
* List<Books> searchBooks(String searchText)
* boolean purchase(Book book, User user)

USER
+ String id
+ String name
+ CreditCard card
```